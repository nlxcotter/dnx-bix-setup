# DRAFT SUBMISSION → build-methods.md

> **Status:** Submitted by TCS-PM 2026-07-14, **teachable-authorized by Cotter.** For GM to curate into `build-methods.md` (single-writer). Verified on TCS 2026 by a real prod incident: an owner admin list that 500'd once its table crossed 100 rows, diagnosed and fixed live; the prevention helpers are deployed. Generalized, no secrets.

---

### N · Scale-safe D1 reads (chunk bulk IN queries, page long lists) so "works small, breaks big" can't ship
*A DashNex/D1 read that filters by a list of ids works perfectly in testing and silently breaks in production the day the table crosses 100 rows. This makes that whole class of bug structurally impossible, and tells you how to catch it if it already shipped.*

**What it does & why it matters.** Cloudflare D1 (the database under DashNex) has a **hard limit of 100 bound parameters per query.** Any read shaped like `WHERE col IN (id1, id2, ...)` sends one parameter per id, so it runs fine with a handful of rows and then **fails every time** once the list passes 100. Because it only breaks at scale, it sails through testing and detonates later in production, on the owner, with no warning. A second, quieter version of the same trap: a **hardcoded page size or limit** (e.g. `list(1000, 0)`) that silently truncates results once the table outgrows it. Both are "works small, breaks big." This method removes both before they can happen.

**The prevention (do this in every build, from day one):**
1. **Never write a raw `inArray(col, ids)` / `WHERE col IN (...)` over a caller-supplied list.** Route every bulk-by-ids read through ONE shared helper that chunks it under the cap. Reference implementation (`src/site/db.ts`):
   ```ts
   // Keeps every query under D1's 100-parameter cap regardless of list size.
   export async function selectInChunks<T>(
     ids: string[], run: (idChunk: string[]) => Promise<T[]>, chunkSize = 90,
   ): Promise<T[]> {
     const out: T[] = []
     for (let i = 0; i < ids.length; i += chunkSize) out.push(...(await run(ids.slice(i, i + chunkSize))))
     return out
   }
   ```
   Call site: `const rows = await selectInChunks(ids, (slice) => db.select().from(t).where(inArray(t.id, slice)).all())`.
2. **Never trust a hardcoded page size to hold "all" of something.** Page the list API to exhaustion so growth can't truncate it:
   ```ts
   export async function fetchAllPaged<T>(
     page: (limit: number, offset: number) => Promise<T[]>, pageSize = 200,
   ): Promise<T[]> {
     const out: T[] = []
     for (let offset = 0, guard = 0; guard < 1000; offset += pageSize, guard++) {
       const batch = await page(pageSize, offset); out.push(...batch)
       if (batch.length < pageSize) break
     }
     return out
   }
   ```
   Call site: `const all = await fetchAllPaged((limit, offset) => svc.listContacts(limit, offset))`.
3. **Chunk size 90, not 100.** Leave headroom under the cap for any extra bound parameters the query builder adds.
4. **Audit at build time, not incident time.** Run `grep -rn "inArray\|IN (" src/` and confirm every hit either filters a single id, or goes through `selectInChunks`. Do the same for any hardcoded `limit(<big number>)` or `list(<n>, 0)`. On TCS this sweep found exactly one offender; everything else read one row at a time.

**⚠️ Landmines / notes:**
- **The failure is invisible by default.** A thrown error in a DashNex route returns a bare **HTTP 500 with an empty `{}` body**, and DashNex exposes **no worker-log retrieval** (checked the CLI: no `logs`/`tail` command). So the bug gives you nothing to go on unless you instrument for it (see the diagnostic below).
- **Some module methods already chunk internally; some don't.** Do not assume. On TCS, `getContactsTagNamesMap(ids)` survived 100+ ids but our own raw `inArray` did not. When in doubt, chunk your own call to the module too, defensively.
- **A retry will NOT fix this.** The param-cap failure is deterministic, not transient, so retrying just fails three times slower. Retry is for genuine transient blips (an occasional D1 read drop), a separate concern; keep the two ideas distinct.
- **Remote `dashnex db migrate --remote` needs a Cloudflare API token you do not hold** (`CLOUDFLARE_API_TOKEN environment variable is required for remote operations`). If a fix needs a new table, have the worker create it itself at runtime with `CREATE TABLE IF NOT EXISTS` (the worker's DB binding has the rights); do not depend on the remote migration path.

**The diagnostic (if the invisible 500 already shipped, make it talk, then fix per above):**
1. **Wrap the handler's data read in try/catch** and return the REAL error in the response instead of `{}`. If the page already renders a debug field, use it; e.g. return `{ _dbg: { message, at } }` with status 500. The owner instantly sees the true cause instead of a blank object.
2. **Persist each failure** to a tiny self-creating log table (`CREATE TABLE IF NOT EXISTS admin_error_log ...` on first use), best-effort and never-throwing, so intermittent failures are captured even when nobody is watching. Add an owner-only `/api/admin/errors` viewer.
3. **Read the captured query.** On TCS the logged error printed the failing SQL with its ~101 `?` parameters, which named the cause (the 100-cap) in one shot. Guessing had wasted a full round; the logged flag ended it immediately.
4. **Then apply the prevention above** and re-verify at the real row count, not a test-sized one.

**The rule:** on DashNex/D1, **a read must be safe at 10,000 rows the day you write it, not the day it breaks.** Chunk every bulk `IN` read (helper, 90 per chunk), page every "give me all of them" list, and when a 500 has no body, make it log the truth before you touch the fix.

---
*Submitted 2026-07-14 by TCS-PM, teachable-authorized by Cotter. Born from a real prod incident: the TCS Clients admin feed 500'd once the client list crossed 100. GM to curate into `build-methods.md`; delete or retire this `.draft.md` once folded in.*
