# Build Methods — DashNex
### Universal how-tos for building an online business on DashNex → the teaching product

> **Home:** the `dnx-biz-setup` project.
> **What goes here:** *only* reusable, **universal** DashNex build-methods — platform how-tos that apply to any business, **not** tied to one project.
> **What does NOT:** how-to-work-with-Cotter → `Systems/canon/working-with-cotter.md` · project-specific work → that project. (See `Systems/canon/governance.md` → Lessons-learned routing.)
> **Write model:** an agent **submits** a method; the **GM curates it in** — never free-append. Single-writer discipline keeps this from drifting into the mess the Canon exists to fix.
> **Purpose:** raw material for the future **teaching product**. Every method here is filed to a **rung of the training spine** (`training-outline.md`) via its **Teaches →** line — this file feeds the modules, it is not a separate pile.

---

## Methods (curated)

### 1 · Set up a paid product in DashNex — Product → Variant → Offer → Checkout → connect
*Verified live 2026-07-02 — the full flow tested end-to-end with a real checkout overlay (Express pay, email pre-filled).*
**Teaches → Module 6 (Monetization).**

**What it does & why it matters.** It turns a dead "buy" button live. In DashNex a sale is just an **entitlement** — you build four things (a Product, a Variant, an Offer, a Checkout), copy **two IDs** into your app, and the button works. All clicks, no billing code. But miss one step or one hidden gotcha and the button just sits greyed out **with no error telling you why** — so follow the order, and watch the three traps at the bottom.

**The four builds, in order:**

**1. Product** — *DashNex → Products → Add product.*
- **Name** it. **Product type → Software** — *so that* it can carry a usage limit (a credit), not just be a one-off download.
- **Variables** box → type the limit's name in snake_case (e.g. `max_reports`). **⚠️ Trap 1 — you MUST press Enter to lock it in.** On desktop the field commits *only* on Enter (the "＋" button is hidden on anything wider than a phone). Typing alone does nothing — the Create button won't even light up. It took when the word jumps into a chip and the box clears.
- Description + Tag → blank. **Create product.**

**2. Variant** — *the product's ⋮ → Manage Variants → Add variant.*
- **⚠️ Trap 2 — the variable's NAME lives on the Product, but its VALUE lives on the Variant.** Two screens for one idea.
- Name the variant, Create, then open it (⋮ → **Edit**): now a **Variables** section shows `max_reports` — set its **value** (e.g. `1`) = how many credits one purchase grants. **Reset Period → None** for a one-time buy. **Update variant.** *(If that field is missing, the variable never stuck on the product — redo Trap 1.)*
- **Copy the Product ID** (Products list → ⋮ → Copy ID).

**3. Offer** — *DashNex → Offers → create.*
- **Internal name** (yours) · **Invoice name** (shows on the buyer's receipt) · **External description** (optional, shows at checkout).
- **Pricing:** Price type **One Time** · Currency · **Price**.
- **Items → ＋ Add item → pick your Product → Variant.** *So that* the sale is tied to the credit — **this link is what makes the money actually deliver the thing.**
- Bump → skip (an add-on upsell for later). Guarantee → optional. **Create.**

**4. Checkout** — *the offer → Checkouts.*
- DashNex auto-creates a **default checkout** (Express pay already on) — use it, no need to make one.
- **⚠️ Trap 3 — the Checkout ID is hidden in the link.** The row's ⋮ offers only *Preview* and *Copy link* — no "Copy ID." Hit **Copy link**; the Checkout ID is the **last segment of the URL** (`…/checkouts/{THIS-part}`). Copy only that tail, not the whole URL.

**Connect it (the payoff).** Paste the **Product ID** and the **Checkout ID** into your app's config (the module's admin). Each **validates on Save** — a typo is caught right there, by you, not by a customer at the till. Both flip to **"Configured" → the button goes live.**

**The three traps, one line each — this is the hour-saver:**
1. **Product variable → press Enter** to commit (hidden "＋", desktop).
2. **Name on Product, value on Variant** (two separate screens).
3. **Checkout ID = the tail of the Copy-link URL** (there is no "Copy ID").

---

### 2 · Send a transactional email on DashNex — and why it silently fails
*Verified live on TCS; teachable-authorized by Cotter 2026-07-06.*
**Teaches → Module 5 (Wiring real services).**

**What it does & why it matters.** It sends an email straight from your app — a receipt, a review request, a notification — with no mail server to run. You call one function and DashNex delivers it. **The trap that eats hours:** if the `from` address isn't a sender or domain **verified inside DashNex**, `send()` returns with **no error at all** and the email simply never arrives. Getting the `to` right is necessary but **not sufficient** — an unverified `from` fails **silently**.

**How to send:**
- `getMailer().send({ from, to, replyTo, subject, html, text })` from `@dashnex/core`.
- Point `from` at a **verified** sender: `const from = process.env.DEFAULT_EMAIL || '<your-verified-address>'`.

**⚠️ The silent-failure trap.** *No error + nothing arrives* === the `from` is unverified. Fix it in DashNex's **email / sender settings** (verify the sender or domain) **before** touching code — no code change helps while `from` is unverified.

**Hooks** (to intercept or customize a send): `core.before_mailer_send`, `core.before_send_transactional`.

**The trap, one line:** unverified `from` → `send()` succeeds silently, mail never lands. Verify the sender first.

---

### 3 · Contacts / CRM on DashNex — the custom-field event trap + safe upsert
*Verified live on TCS; teachable-authorized by Cotter 2026-07-06.*
**Teaches → Module 5 (Wiring real services).**

**What it does & why it matters.** It lets your app read and write DashNex Contacts (your CRM) — create a contact, update fields, tag them — and keep an outbound sync (e.g. to Google Contacts) in step. **The trap that breaks the sync:** DashNex fires **NO event when a custom-field VALUE is written**, so a passive listener meant to trigger your sync **never fires** on those writes — the data saves, but nothing downstream ever hears about it.

**The service + a safe upsert:**
- Service = `getContacts()` from `@dashnex/contacts`. **Email is the unique identity key.**
- Upsert: `getContactByEmail` → `updateContact` or `createContact`.
- A custom field needs a **definition (label + key)** before values can store; the **values** live in a separate `contactFieldValues` store (keyed by contactId + fieldKey), which is why no event fires for them.

**⚠️ The event trap.** DashNex emits events for contact **created / updated / deleted / get** and the **tag lifecycle** — but **nothing for a custom-field-value write.** So any outbound sync or side-effect must be **pushed explicitly at the write site**, right after you write the field — a listener won't catch it.

**Corollaries (each a real trap):**
1. **Push by known contact ID, not an email-upsert** — a re-push keyed on email spawns downstream **duplicates**.
2. **Blank-wipe guard — "blank = keep."** Patch only non-blank fields, so a partial re-submission can't **erase** stored data.
3. **Tag ops DO emit events** — safe to drive side-effects off tag add/remove (unlike custom-field values).

---

### 4 · Scale-safe D1 reads — chunk bulk `IN` queries, page long lists
*Verified on TCS by a real prod incident 2026-07 (an admin list that 500'd once its table crossed 100 rows); prevention helpers deployed. Teachable-authorized by Cotter.*
**Teaches → Module 4 (The review discipline), with Module 3 (Building in phases).**

**What it does & why it matters.** Cloudflare D1 (the database under DashNex) has a hard limit of **100 bound parameters per query.** Any read shaped `WHERE col IN (id1, id2, …)` sends one parameter per id, so it runs fine with a handful of rows and then **fails every time** once the list passes 100 — invisible in testing, detonates later in production, on the owner. A quieter twin: a hardcoded page size (`list(1000, 0)`) that silently truncates once the table outgrows it. Both are "works small, breaks big."

**The prevention (from day one):**
- Never write a raw `inArray(col, ids)` over a caller-supplied list. Route every bulk-by-ids read through ONE shared chunking helper (`selectInChunks`), **chunk size 90** (headroom under the cap).
- Never trust a hardcoded page size to hold "all" of something — page the list API to exhaustion (`fetchAllPaged`).
- **Audit at build time, not incident time:** `grep -rn "inArray\|IN (" src/` and confirm every hit is a single id or goes through the helper; do the same for any big hardcoded `limit()` / `list(<n>, 0)`.

**⚠️ Landmines:**
- **The failure is invisible by default** — a thrown error in a DashNex route returns a bare **HTTP 500 with an empty `{}` body**, and DashNex exposes **no worker-log retrieval**. Instrument it: wrap the read in try/catch and return the *real* error; persist failures to a self-creating `admin_error_log` table with an owner-only viewer.
- **A retry will NOT fix it** — the cap failure is deterministic, not transient. Retry is a separate concern (genuine transient blips only).
- **Some module methods chunk internally, some don't — don't assume; chunk your own call defensively.**

**The rule:** on DashNex/D1, a read must be safe at **10,000 rows the day you write it, not the day it breaks.**

---

### 5 · Know your lane — escalate DashNex PLATFORM bugs (with a clean bugcheck), fix only your own code
*Born from a real incident 2026-07: a dev-server crash first mis-approached as "downgrade the owner's Node." Teachable-authorized by Cotter. Classified PM-Teachable.*
**Teaches → Module 4 (The review discipline).**

**What it does & why it matters.** You own your app code (`src/`); you **cannot see DashNex's platform code**, and their docs are thin. So for any failure in THEIR layer (dev server, CLI, Node/runtime crashes, module internals, deploy pipeline) you are working blind — and guessing plus invasive action breaks more than it fixes. The disciplined move: **fix your own code, escalate their platform with a clean bugcheck.**

**Draw the line first:**
- **YOUR code** (a query, a page, a route, a repo config) = yours to fix.
- **THEIR platform** (dev-server crashes, CLI errors, runtime incompatibility, module `dist` internals, deploy internals) = escalate. Do NOT self-fix.
- **Can't tell whose it is? Treat it as theirs and escalate** — cost of wrongly escalating is a support ticket; cost of wrongly self-fixing is a broken environment and lost trust.
- **Never modify the owner's system unilaterally** (Node version, global npm, package manager). Their machine, their call — let support say the fix first.

**The bugcheck report support actually needs:** versions (CLI + its declared `engines`; Node + how it was installed; OS/arch; the crash-site dependency + version) · a one-sentence symptom + what still works · the verbatim stack (ANSI stripped) · minimal repro (+ does it survive clean restarts) · your analysis (what you can infer without their source) · pointed questions only they can answer.

**⚠️ Landmines:**
- **Never declare a platform bug "fixed" without proof** — a recurrence costs more trust than the original bug. If it's intermittent, say intermittent.
- **Distinguish a bug YOU introduced from a platform incompatibility** — corrupted `node_modules` from a bad install is yours (own it, rebuild clean); a too-new Node major is an escalation, not a hack.
- **Newer is not always better** — a bleeding-edge Node major installed silently by a package manager can break a CLI whose `engines` floor allows it but was only tested on the LTS.
- **The bugcheck report IS the deliverable when the problem is theirs — not a failure.**

**The rule:** fix what you can see (your code), escalate what you can't (their platform), never backtrack the owner's system on a guess.

---

### 6 · Build an effective blog article for a local-business site
*Verified live across the TCS blog, 20+ published posts, 2026. Teachable-authorized by Cotter.* **Also Client-Teachable** (owner-course material — spine Module 8).
**Teaches → Module 8 (Selling it), with Module 6 (Monetization).**

**What it does & why it matters.** A local-business blog post is a **customer's real question answered honestly** — which ranks in search, feeds AI answer engines (via schema + `llms.txt`), and earns the trust that makes the reader call *you*. Done loosely, posts read like filler and convert nobody. Done with a system, each one is a small, reusable asset.

**The process, in order:**
1. **Queue an idea, don't free-write** — one file per topic naming the exact customer question, the search intent, which services it drives, and why now.
2. **Interview the owner BEFORE writing** — the single biggest quality lever. Build on the owner's bench truth (real prices, war stories, the misconceptions they see daily), not generic web research. ⚠️ **Never publish web-research claims the owner hasn't confirmed** — on TCS a "generally reliable" claim was flat wrong per 20 years on the bench; his correction became the post's strongest section.
3. **Draft as unpublished (WIP)** — `published:false` so it renders at its URL for preview but stays out of the index, sitemap, and `llms.txt` until ready.
4. **Ship deliberately** — typecheck → deploy → verify live (curl the URL + hero + confirm it's indexed) → commit/push → shut the dev server down.

**The craft standards:** title = **search intent, not clever** (how a worried person would say it) · a **reusable component kit built once** (TL;DR box, boxed Pro-Tip callout, FAQ block wired to FAQ schema, hero image, CTA, related-reading links, Article + FAQ JSON-LD) · **6th-grade GNAT reading level** (Canon `gnat-standard.md`) — jargon is invisible to the writer, define every term on the spot or cut it · warm, plainspoken, honestly-expert voice in the owner's words · emphatic safety routing to a licensed pro on physical-hazard topics · internal links to related posts + the money pages.

**The GBP companion (do this for every post):** a condensed same-day Google Business Profile post · ⚠️ **ZERO contact info in the body** (no phone/URL/email — the link goes only in the "Learn more" button) or Google auto-rejects it · under ~1,500 chars, lead with the hook, restrained emoji, schedule it after the post is live so the button never 404s.

**The rule:** interview the owner first, write at a 6th-grade level, keep it WIP until verified live, and never ship a claim the owner hasn't confirmed.

---

### 7 · Add an InstraChat AI chatbot to a DashNex site — create → train → guardrail → PUBLISH → verify
*Born from a real end-to-end build (the TCS Assistant on computerrepairdurango.com), 2026-07. Teachable-authorized by Cotter. Classified PM-Teachable, with two Client-Teachable spillovers flagged.*
**Teaches → Module 5 (Wiring real services).**

**What it does & why it matters.** InstraChat is DashNex's built-in chatbot module. On a DashNex webapp you do **not** hand-code an embed — the module auto-injects the widget loader client-side via the `system.scripts_render` hook (your layout's `<RenderScripts />`), and the loader asks `/api/instrachat/enabled-agent` who the site agent is before drawing the bubble. So 100% of the work is dashboard config, and every failure mode is "a setting you didn't know existed is off." Use only the official module and its Chat Bubble toggle; never hand-roll an embed or write the module's DB tables to skip a step.

**The ordered flow:** Create Agent → Add Sources (crawl the site) → Add Q&A (guardrails; override sources) → **PUBLISH** (the hidden switch) → Appearance → Verify in production.

**Field caps that bite immediately:** Agent **Name = 30 chars** · **Base Instructions = 255 chars** (a tiny system prompt — identity + tone + top guardrail + a next-step; the real enforcement lives in Q&A) · **Q&A Answer ≈ 255 chars** (count before pasting — it truncates mid-word).

**Sources:** crawl from the site root, **leave "Include Only Paths" EMPTY** (filling it silently drops everything else, e.g. the homepage) · **Exclude** admin/api/form routes (`/admin, /api, /checkin`) · **set a Recrawl period** (default is Never → the bot goes stale) · ⚠️ **Recrawl refreshes KNOWN URLs only — brand-new pages need a fresh Bulk-URL crawl to be discovered** · **Added ≠ Indexed** — indexing is automatic on a ~10-min cycle, there is **no manual index button**; don't trust the ✗ column, test in the Playground.

**⚠️ PUBLISH — THE trap of this build.** There are **two separate switches, and they are NOT next to each other:**
- **"Active"** (toggle at the top of the agent view) = the agent is alive and allowed to answer. **This is NOT "show on the website."**
- **"Chat Bubble"** (a toggle *inside the Integration tab* — the **`</>` code icon**) = what actually publishes the bubble to the site.
- `/api/instrachat/enabled-agent` returns `null` until Chat Bubble is on. The agent must be **BOTH Active AND Chat-Bubble-on.** In any owner walkthrough, name the `</>` icon explicitly — it's the last place a non-technical owner looks. (Ignore the "Widget Integration Code" box on that tab — that's only for embedding on a *non-DashNex* site.)

**⚠️ Custom app-router pages (learned the hard way).** On a site with hand-built native app-router pages, the auto-inject can silently fail to reach those pages — agent Active, `enabled-agent` returns it, yet **no bubble renders.** Diagnose: grep the live JS bundles for `InstraChatConfig` / `instrachat-widget` / `enabled-agent` — **zero hits = the injector never shipped to those pages.** Fix: paste the dashboard's **Widget Integration Code** snippet into the site `<head>`/layout (the official embed; loads the worker-served widget JS regardless of whether auto-inject reaches your pages). Verify after deploy: the loader strings now appear in the raw HTML.

**Verify in PRODUCTION (dashboard ≠ site):** `curl -s https://SITE/api/instrachat/enabled-agent` returns your agent's `{id, name}`, not `null` · then load the live site on desktop + phone, confirm the bubble renders and answers and doesn't collide with a sticky mobile call/CTA bar (`[id$="-bubble"]{bottom:<barheight+gap>px!important}` under the bar's breakpoint).

**Client-Teachable spillovers (owner-course language, Module 5):** "train the bot on the website you already built — your site IS the bot's brain," and "decide what your bot must NEVER say, and lock it in Q&A."

**The rule:** a DashNex chatbot goes live only when the agent is **both Active AND Chat-Bubble-enabled** AND the loader actually ships to the page — verify with a live `enabled-agent` check **and** a grep of the live HTML, never the dashboard alone.

---

## Platform facts and limits (constraints to design around, not methods)

### DashNex credits are per-Business and do NOT transfer between Businesses
*Confirmed by DashNex support 2026-07-28 (Cotter asked directly). Teachable-authorized by Cotter.*
**Teaches → Module 1 (Foundation & setup).**

Credits live inside a single DashNex **Business** and cannot be moved to another one. A second Business starts from zero, and there is no way to shift a balance over. **Plan your account structure before you split anything.** If a client, a brand, or a side venture might run its own DashNex Business, decide up front where the credits should sit, because you cannot rebalance later.

**⏳ Open (watch):** Cotter has a pending question with support on whether this becomes possible under the **new DashNex ID system.** Treat "no transfer" as CURRENT, not permanent, and update this line when support answers.

---
*Stood up 2026-07-01 by the GM. Methods 2–3 curated 2026-07-06; methods 4–7 curated 2026-07-23 (submitted from the TCS lane 07-06 → 07-21, teachable-authorized by Cotter). Platform-facts section added 2026-07-28 (DashNex credits are per-Business, teachable-authorized by Cotter). Every method is filed to a rung of the training spine (`training-outline.md`). Dev-server-preview is a submitted method held on the back burner pending DashNex Support on the TCS dev-server incident. Governed by `Systems/canon/governance.md`.*
