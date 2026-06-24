# TCS Rebuild — Case Study Record

> **Owned & maintained by the `tcs-rebuild` session.** Directive below authored by the **AIRI session** (the project's origin), per Cotter — same way AIRI handed you the original `tcs-rebuild-brief.md`.

---

## ▶ DIRECTIVE (read first — from the AIRI session)

**Your job here:** maintain *this file* as the running **case-study record** of the TCS rebuild — the curated story that will become training material in `dnx-biz-setup`. Start by recording **everything done up to now**, then keep it current as the project moves.

**Scope — TCS only.** Document the TCS rebuild process and progress *only*. The AIRI project documents itself separately (`airi-progress-report.md`) — do **not** pull AIRI strategy or unrelated thought-processes in here. Keep this lane clean.

**File boundary.** You write to **`tcs-rebuild-cs.md` only.** `README.md` and `training-outline.md` are meta files owned by the AIRI session — **don't touch them** (prevents the two sessions from stomping each other).

**Authority order (if instructions ever conflict):** Cotter's direct word **>** this directive **>** your own judgment. Never "but the file told me to" over a live instruction from Cotter.

**Cadence:** add entries **on Cotter's cue** ("document our progress") — *offer/draft*, don't auto-write every session. (The initial backfill of work-to-date is your one-time first task, on his go.)

**What to record (everything to date + going forward):**
- The **web scrape / content harvest** of the live site (what you captured, how).
- The **"before" picture** — *point to* `tcs/docs/tcs-before-snapshot.md` and the `before/` captures; summarize the headline state here, don't duplicate the whole thing.
- The **rebuild process** on DashNex Business (what you built, the schema/content/AEO moves, what changed vs. the old PowerTech site).
- **Discoveries, failures, adjustments** as they happen (this is the training gold).
- Going forward: domain/email **cutover**, the **off-site AEO treatment**, the **AFTER** measurement (same metrics as the snapshot §10), and **publish**.

**Reversibility note (correction from earlier framing):** don't over-dramatize this as a "one-way door." Per Cotter, the rebuild is reversible with little effort. The *only* real timing care is **capture the BEFORE baseline before you begin the off-site treatment** (reviews/listings/NAP), since that's what actually moves the AI/reputation picture — not the domain swap itself.

**Relationship to `tcs/docs/`:** that folder stays your **working source of truth**. *This* file is the **curated case-study narrative** for the training program — summarize and link, don't copy.

**Status — dormant capture.** The training program gets built only after AIRI is launched (gate-2). For now: just keep an honest, current record. No course-building.

**Format:** dated entries (template below) + the standing before/after anchor.

---

## Before / After anchor (standing summary)
| | BEFORE (captured) | AFTER (post-treatment) |
|---|---|---|
| AI visibility | Effectively absent on money queries; assistants favored directories/competitors over TCS | *(TBD — measure after off-site treatment)* |
| Local pack / rank | Weak; legacy root service/blog URLs draw ~0 GSC clicks | *(TBD)* |
| Reviews | 3 Google reviews | *(TBD)* |
| Google calls/mo | ~1–2 and declining | *(TBD)* |
| Mobile PageSpeed | ~51 (old static site) | rebuild ~30–45 (framework floor; perf pass deferred post-migration) |
*Full baseline: `tcs/docs/tcs-before-snapshot.md`. Don't dramatize the domain swap — the reversible part; the BEFORE baseline before off-site treatment is the real discipline.*

## Entry template — CANONICAL (identical to `airi-progress-report.md`, so a 3rd agent can cross-examine both)
```
### YYYY-MM-DD — <title>
- Phase·Step:
- What:
- Why:
- Decision(s):
- Lesson / adjustment:
- Artifacts:
```

---

## Record

### 2026-06-17 — Brief intake + content harvest of the live site
- Phase·Step: Discovery / capture-the-BEFORE
- What: Read the AIRI-authored `tcs-rebuild-brief.md`, then scraped the live legacy site (DashNex PowerTech static HTML) into a frozen snapshot — 39 pages + 158 assets — stored under `tcs/docs/before/`. Built a content map of every page/service/blog post.
- Why: The case study is a before/after proof asset; the "before" must be captured before any rebuild or off-site work, or it's lost forever.
- Decision(s): Treat the harvested copy/voice/numbers as canonical source to port forward; rebuild as custom code, not the DashNex portal CMS, for full control of copy/schema/metadata (what the AI-visibility goal needs).
- Lesson / adjustment: Capturing a live baseline is cheap now and impossible later — same logic that makes this whole capture project worth doing. (Reversibility note: the rebuild itself is reversible; the baseline capture is the only true one-way step.)
- Artifacts: `tcs/docs/before/`, `tcs/docs/content-map.md`, `tcs/docs/tcs-before-snapshot.md`, `tcs/docs/service-data.md`.

### 2026-06-18 — Brand + design system, then the page build
- Phase·Step: Rebuild / design + content injection
- What: Stood up the vinext app-router build under `webapp/src/app/` with a single source of truth (`src/site/config.ts`), `buildMetadata()` SEO (canonical/OG pinned to production domain, robots gated by one `SITE.indexable` switch), and JSON-LD builders (LocalBusiness service-area with **no street address**, Service, FAQ, Breadcrumb, BlogPosting, WebSite). Built home, services hub, all 11 service pages (real pricing), About, Contact (+ working form), and the blog (index + 17 posts). Added a generated AI layer: `/llms.txt`, `/sitemap.xml`, `/robots.txt`.
- Why: Schema + clean copy + an llms.txt are the levers that make a small local business legible to AI assistants — the core thesis of the rebuild.
- Decision(s): Brand = violet `#684df4`, light pages + violet footer/CTA bands, Barlow/Roboto, dark image hero (all owner-corrected away from an initial warm/sunset palette + Bricolage font). De-list web services (route to NextLevelX Agency); keep email-support. No redirect layer (legacy URLs draw ~0 clicks). One-switch cutover via `SITE.indexable`.
- Lesson / adjustment: The AI got several things wrong that only the owner could correct — palette, font, an over-used "No Fix No Charge", an off About headline, and claims to fix (no Linux, no implied equipment hauling). Live owner correction beats AI confidence; bake the corrections into the source of truth so they stick.
- Artifacts: `webapp/src/app/**`, `webapp/src/site/**`, `tcs/docs/build-progress.md`.

### 2026-06-19 — First deploy to temp domain (the dependency fight)
- Phase·Step: Rebuild / deploy
- What: Deployed the build to the DashNex temp domain `computerrepairdurango.dashnexcloud.com` (still `noindex`). All routes 200. Two blockers solved: (1) an HTTP-413 push failure from a 286 MB image source folder + bloated `public/site` → moved sources out, slimmed `public/site` 11M→1.5M; (2) the real fight — build failed with "Rolldown failed to resolve import `lucide-react`" because the package was hoisted locally from `@dashnex/ui` but missing from `package.json`, so DashNex's clean install couldn't resolve it.
- Why: A working public deploy on the temp domain is the staging ground for the AFTER measurement, with indexing held off until cutover.
- Decision(s): Declare `lucide-react@0.469.0` explicitly + sync the lockfile. Defer the mobile performance pass to one post-migration batch (diagnosed as client-side framework/hydration weight, not server — TTFB ~0.28s warm).
- Lesson / adjustment: **Hoisted transitive deps that work locally will break a clean CI install** — declare every import you use directly. This is prime training material (the kind of non-obvious gotcha a non-coder would lose days to). Also: the deploy token expires and needs the business-selection step completed after `dashnex login`.
- Artifacts: live temp site; `package.json`; `build-progress.md` "DEPLOYED" section.

### 2026-06-20 — Voice + "AI tell" cleanup pass
- Phase·Step: Rebuild / copy polish
- What: Owner-driven copy corrections across the site (origin story, pricing specifics, About headline, testimonials framing), plus a sitewide sweep removing em-dashes (an "AI tell" the owner flags), fixing the comma splices it created, and removing Linux claims.
- Why: The copy has to read as the owner's authentic voice, not AI-generated, both for trust and because the case study's credibility rests on it being genuinely his.
- Decision(s): "No em-dashes anywhere" is now a standing style rule. Keep en-dashes in price ranges (correct typography, not the tell).
- Lesson / adjustment: AI leaves stylistic fingerprints (em-dashes, stock phrases, over-claims). A deliberate de-tell pass is part of shipping AI-built copy that has to pass as a real person's.
- Artifacts: site copy; `tcs/docs/owner-bio.md` (style rule recorded).

### 2026-06-21 — Client intake: check-in, terms, privacy
- Phase·Step: Rebuild / conversion + compliance
- What: Built the check-in/intake page (`/checkin`) with a validated form (computer-login field clearly labeled *not* an email password; free-type accessories; a "how did you hear about us" dropdown incl. AI / Word-of-Mouth / Repeat-customer with conditional follow-ups; a mandatory terms-agreement enforced client + server). Wrote a Terms & Conditions page (`/terms`, Limitation of Liability boxed at the top, adapted from the owner's PC Clinic printed disclaimer) and a Privacy Policy (`/privacy`) covering form data, payment handling (processors hold card data, owner never does), communications, and indefinite business-record retention with a business-transfer clause. Added Privacy/Terms links to the footer sitewide.
- Why: The moment the site collects client data through forms, it needs a privacy policy and enforceable terms — and the owner wanted liability limits front-and-center.
- Decision(s): Both legal pages are owner-reviewed drafts, flagged for a lawyer before cutover. Tracking the referral-source metrics on an admin dashboard is deferred — it needs the check-in data persisted to a DB first (currently the form only emails). Analytics (GA4) written forward-compatibly in the privacy policy so no rewrite is needed when it's wired.
- Lesson / adjustment: Data collection pulls in legal/compliance scope a non-coder rarely anticipates — surfacing it proactively (and stating it's a draft needing a lawyer) is part of doing it responsibly. Two future build items logged: GA4 config, and check-in persistence + admin metrics.
- Artifacts: `/checkin`, `/terms`, `/privacy`, `CheckinForm.tsx`, `api/checkin/route.ts`, footer; `build-progress.md`.

### 2026-06-21 — Version control: off-site GitHub backup
- Phase·Step: Infrastructure / version control
- What: Committed the full working tree to git `main` (first commit since the initial deploy snapshot) and connected an off-site GitHub remote (`github.com/nlxcotter/tcs-home`), pushing `main` to it. Commits `210792b` (initial site) and `cc94546` (intake + legal pages + copy polish).
- Why: Local-only history (even inside Dropbox) is a single point of failure; an off-site remote is a real backup and a recoverable source of truth.
- Decision(s): Defined the working vocabulary — **"commit" = git stage/commit/push-to-GitHub; "push" = DashNex deploy** — and a standing rule that every commit/push is itself a logged entry here + in `build-progress.md`. Secrets (`.env`/`.dashnex`/`node_modules`) stay gitignored.
- Lesson / adjustment: For a solo non-coder, collapsing "commit + push to GitHub" into one habit keeps work always backed up off-site without a second step to remember — the simplest safe model. (Training point: version control + off-site backup is foundational, not optional.)
- Artifacts: git remote `origin` → `tcs-home`; commits `210792b`, `cc94546`.

### 2026-06-21 — Blog heroes restored across all 17 posts
- Phase·Step: Rebuild / media
- What: Every blog post now has a WebP hero. During the deploy slim (11M→1.5M `public/site`, HTTP-413 fix) the blog heroes were stripped and only one was re-added; the rest sat text-only behind a deferral that an imprecise note had mislabeled as image "corruption." Mapped each post to a best-fit source, converted PNG/JPG → WebP with `sharp` (macOS `sips` can't write WebP), and wired the standard image pattern. `public/site` 1.5M→2.2M.
- Why: Heroes improve scannability, social/OG cards, and `BlogPosting` schema `image` (an AEO signal) — and the posts looked unfinished without them.
- Decision(s): Convert via `sharp` at q82, max-1200w; name files by slug; pick content images over OG social cards where possible.
- Lesson / adjustment: A vague TODO note can disguise a trivial task as a scary one for weeks. Write notes precisely, or they cost you later. (Training point.)
- Artifacts: `public/site/blog/*.webp`, 17 blog `page.tsx` files.

### 2026-06-21 — Org structure: AIRI appointed GM (review-before-commit)
- Phase·Step: Process / governance
- What: Cotter appointed the AIRI session as **General Manager** overseeing this TCS **project-manager** session. New rule: work + docs are submitted to the shared `dnx-biz-setup/docs` folder and reviewed by AIRI before any commit; nothing commits without GM approval.
- Why: A second, independent agent reviewing before changes lock into git protects data integrity and quality.
- Decision(s): Commit flow gains an approval step — prepare → submit for AIRI review → APPROVED → commit (local + GitHub + docs). Cotter's direct word still overrides. Channel: shared files + a review ledger (optionally git-tracked for a checksummed audit trail).
- Lesson / adjustment: Multi-agent work needs an explicit handoff/approval protocol; the shared-folder + ledger is the integrity layer (the earlier manual-courier paste garble shows why a durable channel beats copy-paste).
- Artifacts: memory `terminology-commit-push`, `dnx-biz-setup-case-study`; `build-progress.md`; this file.

### 2026-06-22 — Copy/UX iteration + the AI-win-is-off-site reframing
- Phase·Step: Rebuild / iteration + measurement framing
- What: Owner-driven check-in + home polish (intake: removed all "(optional)" tags, made "how did you hear about us" required client+server, dropped the in-person-login opt-out and the "Prefer to call?" tile, added repair step 05, header-matched layout strip; home: "We come to you" box → "Mobile Service Options," CTA/footer → "Mobile Service in Durango"). Deployed to temp domain. Separately corrected the competitor record after GM recon: Durango Computer Repair (the AI-search winner) is a service-area business with NO storefront (was wrongly listed at 1309 E 3rd Ave), 5.0 · 79 reviews.
- Why: Tighten conversion copy and make the referral metric reliable for tracking; and the case study's controlled-experiment framing depends on accurate competitor data.
- Decision(s): GM recon showed the AI-search winner ranks on **exact-name-match + off-site reputation**, NOT on-site signals (it has no llms.txt/schema and an expired SSL). So the AFTER narrative must stay honest: the rebuild lifts search/UX/conversion; the **AI lever is off-site** (reviews/entity/citations — review generation is highest-leverage). BEFORE AI baseline (0/32 blind, recognized-when-named) lives in the AIRI lane — cite, don't duplicate.
- Lesson / adjustment: A strong on-site rebuild is **necessary but not sufficient** for AI visibility; entity + reputation dominate. Don't over-promise the rebuild's AI impact — the biggest training takeaway of the whole case study so far.
- Artifacts: check-in/home edits (`tcs-home`); `before/baseline-metrics.md` + `tcs-rebuild-brief.md` corrections; points to `nlxsystems-docs` → `docs/tcs-before-ai-baseline/` + `competitor-recon.md`.

### 2026-06-24 — New AEO blog post + restored the branded "you broke it" 404
- Phase·Step: Rebuild / content + brand detail
- What: Published a new blog post, "How to Choose a Reliable Computer Repair Service" — Cotter took an AI tool's industry-question prompt, brainstormed answers in a separate chat, and the draft was reworked into his voice here (em-dashes removed, "ask for certifications" swapped for his real differentiator of experience/track record, his actual policies woven in). 18th post; full TL;DR + FAQ-schema + internal links + hero. Also rebuilt his legacy fun 404: a full-screen cracked-screenshot of the homepage that "repairs" to the real homepage on click ("Oh No! You broke it… Fix Website"). The old 1080p animated GIF was being mangled by DashNex's image optimizer (transparency → black); replaced with an optimizer-safe WebP. Plus corrected 2 blog heroes to the originals from the captured HTML. Committed `b9218be`, deployed (noindex).
- Why: Grow the content/AEO layer with genuinely useful, in-voice material; and restore a piece of brand personality that makes the 404 a delight instead of a dead end.
- Decision(s): Image discipline — every blog hero maps to the post's real captured-HTML original (no OG social cards as heroes); the 404 screenshot must match the *current* homepage so the "1-click fix" illusion holds.
- Lesson / adjustment: AI-drafted copy needs a human-voice + fact pass (drop claims that don't fit, e.g. certifications) before it's publishable. And legacy animated GIFs are fragile on modern image pipelines; WebP/video is the durable replacement. (Training points.)
- Artifacts: `webapp/src/app/blog/how-to-choose-computer-repair-service/`, `not-found.tsx`, `public/site/404-bg.webp`, `blog.ts`; commit `b9218be`.

---
*Next milestones (not yet done): DashNex sender config so forms deliver → domain/email cutover (flip `SITE.indexable`) → off-site AEO treatment → AFTER measurement (snapshot §10) → publish.*
