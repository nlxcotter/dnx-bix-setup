# DRAFT SUBMISSION → build-methods.md

> **Status:** Submitted by TCS-PM 2026-07-09, **teachable-authorized by Cotter.** For GM to curate into `build-methods.md` (single-writer). Verified on TCS 2026: the host-app path works daily; the module-page path is a documented dead end. Generalized, no secrets.

---

### N · Preview a DashNex page on the local dev server (and when NOT to)
*Works flawlessly for host-app pages; the linked-module path is a rabbit hole — this tells you which is which so you don't lose an afternoon.*

**What it does & why it matters.** Lets the owner eyeball a page on `dashnex dev` (localhost) before it goes live. Reliable and fast **for host-app pages**. For **linked-module pages it is not**, and knowing the difference up front saves real time.

**The process (host-app pages — app-router pages under `webapp/src/app/`):**
1. **On request only.** Don't spin up dev proactively; shutting it down is a SHIP step, not an afterthought.
2. **Launch** from `webapp/`: `dashnex dev` in the background, logs to a temp file. Guard: if `lsof -ti tcp:3000` already has it, don't start a second one. (`dashnex dev --https` runs it over HTTPS if a page needs it.)
3. **Find the port** (lands on 3000, falls to 3001/3002). The log's port line is wrapped in ANSI color codes — strip them (`sed 's/\x1b\[[0-9;]*m//g'`) or just read the `lsof` PID.
4. **Verify it serves** before handing it over: `curl -s -o /dev/null -w "%{http_code}" http://localhost:<port>/<path>` = **200** (plus any hero/asset).
5. **Hand over the exact localhost URL.** Edits **hot-reload** — refresh, no restart. Typecheck after each edit (`dashnex check`, or `npx --no-install dashnex check` to avoid a re-fetch).
6. **Shut down as a ship step:** deploy → verify live → commit → **kill dev**. Killing by name isn't enough (it spawns vite/workerd children that keep holding the port): `pkill -9 -f dashnex`, then `lsof -ti tcp:3000 | xargs kill -9`, then curl the port and confirm **DOWN (HTTP 000)**.

**⚠️ The module-page rabbit hole (don't fight this locally).** Host-app pages (in `webapp/src/app/`) preview perfectly. **Linked-module pages** — pages provided by a module living in `packages/*` — are **NOT viably previewable on local dev.** The landmines, all confirmed:
- **`dashnex install` can quietly break your dev server.** It runs a **production install** (strips devDependencies like `cross-env` that `dashnex dev` needs) and, per the DashNex CLI docs, **auto-detects pnpm vs npm from the lockfile present**. Two failure modes follow: (a) if a project carries BOTH a `package-lock.json` and a `pnpm-lock.yaml`, detection is ambiguous, **keep exactly ONE lockfile** (delete the stale one, verified fix) so it can never pick the wrong tool; (b) modern npm **blocks native build scripts by default** (`workerd`, `esbuild`, `sharp`), so those binaries silently never get set up.
- **`link:` is pnpm-only.** npm apps (`package-lock.json`) throw `EUNSUPPORTEDPROTOCOL` — use `file:` or a manual symlink.
- **The module's `@/` alias collides with the host tsconfig** (`@/ -> host/src`), giving a **500 in source mode.** Fix: **use relative imports (or a distinct alias, not `@/`) inside the module.**
- **Module pages are auth-gated** — you must be logged into the local instance to view one.
- **A `'use client'` module page may serve 200 but paint blank** (hydration).

**⚠️ RECOVERY — if a bad install breaks local dev** (classic symptom: an `undici`/`JSON.parse` crash on `dashnex dev` startup while the code typechecks fine and the live site works). The cause is almost always an **inconsistent `node_modules`**: an install run *on top of* a broken tree **reconciles** instead of **rebuilds**, so it stays broken. Do NOT reinstall on top of it. Rebuild from a clean slate — **one cycle, then stop**:
1. `rm -rf node_modules` (delete the hybrid mess entirely).
2. Reinstall with the project's package manager, keeping devDeps + handling peers: `npm install --legacy-peer-deps` (npm, if there's a peer conflict) or `pnpm install` (pnpm).
3. `npm rebuild esbuild workerd sharp` — force the blocked native build scripts to actually run.
4. `dashnex build`, then start dev **once** and curl a page.
If that one clean cycle doesn't fix it, **STOP and escalate — don't loop** (this was a real, expensive lesson).

**Prevention:** one package manager, one lockfile per app. `node_modules` is disposable (gitignored); the lockfile is the source of truth, keep it single and clean.

**The rule:** local dev preview is for **host-app pages**. For **linked-module pages, preview on a DEPLOY** (a staging/preview push) — not local dev.

---
*Submitted 2026-07-09, revised 2026-07-10 (added node_modules recovery recipe after a real dependency-churn incident) by TCS-PM, teachable-authorized by Cotter. GM to curate into `build-methods.md`.*
