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
- Decision(s): GM recon (Durango Computer Repair, one market, **n=1 observational — a proof-of-concept for TCS's situation, NOT a universal law**) shows the AI-search winner's *recommendation* edge is **off-site** (exact-name-match + reviews + GBP), despite a thin on-site footprint (no Identity schema, expired SSL). Frame it in **two layers, and don't dismiss on-site:** Layer 1 = *recommendation* (who the AI suggests — off-site-driven here) vs Layer 2 = *representation accuracy* (when the AI names you, is it correct + does it cite you — where on-site GEO/schema **plausibly helps**; the AI was seen confabulating TCS's details). So the rebuild lifts search/UX/conversion **+ Layer-2 accuracy**; the *recommendation* move is off-site (reviews = highest leverage). BEFORE AI baseline (0/32 blind, recognized-when-named) lives in the AIRI lane — cite, don't duplicate.
- Lesson / adjustment: A strong on-site rebuild is **necessary but not sufficient** for AI *recommendation*, yet on-site GEO is **not irrelevant** — it's the representation-accuracy layer. And this is **n=1**: a proof-of-concept for TCS, not a proven universal rule (scale data is a future AIRI output). Don't over-promise the rebuild's AI impact, and don't over-generalize the finding.
- Artifacts: check-in/home edits (`tcs-home`); `before/baseline-metrics.md` + `tcs-rebuild-brief.md` corrections; points to `nlxsystems-docs` → `docs/tcs-before-ai-baseline/` + `competitor-recon.md`.

### 2026-06-24 — New AEO blog post + restored the branded "you broke it" 404
- Phase·Step: Rebuild / content + brand detail
- What: Published a new blog post, "How to Choose a Reliable Computer Repair Service" — Cotter took an AI tool's industry-question prompt, brainstormed answers in a separate chat, and the draft was reworked into his voice here (em-dashes removed, "ask for certifications" swapped for his real differentiator of experience/track record, his actual policies woven in). 18th post; full TL;DR + FAQ-schema + internal links + hero. Also rebuilt his legacy fun 404: a full-screen cracked-screenshot of the homepage that "repairs" to the real homepage on click ("Oh No! You broke it… Fix Website"). The old 1080p animated GIF was being mangled by DashNex's image optimizer (transparency → black); replaced with an optimizer-safe WebP. Plus corrected 2 blog heroes to the originals from the captured HTML. Committed `b9218be`, deployed (noindex).
- Why: Grow the content/AEO layer with genuinely useful, in-voice material; and restore a piece of brand personality that makes the 404 a delight instead of a dead end.
- Decision(s): Image discipline — every blog hero maps to the post's real captured-HTML original (no OG social cards as heroes); the 404 screenshot must match the *current* homepage so the "1-click fix" illusion holds.
- Lesson / adjustment: AI-drafted copy needs a human-voice + fact pass (drop claims that don't fit, e.g. certifications) before it's publishable. And legacy animated GIFs are fragile on modern image pipelines; WebP/video is the durable replacement. (Training points.)
- Artifacts: `webapp/src/app/blog/how-to-choose-computer-repair-service/`, `not-found.tsx`, `public/site/404-bg.webp`, `blog.ts`; commit `b9218be`.

### 2026-06-24 — Tracked "Remote Help" redirect (outbound-click tracking pattern)
- Phase·Step: Rebuild / utility + measurement
- What: Added a header "Remote Help" button (white/inverse, beside the phone CTA) that points at an internal `/remote` page which loads, then client-side redirects to the RemotePC tool in a new tab. Committed `5724a84`, deployed (noindex).
- Why: Cotter sends clients this link mid-call; routing through `/remote` makes the outbound click a trackable pageview (you can't measure a raw external link).
- Decision(s): Client-side redirect, NOT a server 307 — a 307 skips the page so analytics never sees it; loading `/remote` then bouncing is what makes the hop countable (once GA is wired, #11). New tab so the customer keeps their place on the site.
- Lesson / adjustment: To measure clicks that leave your site (affiliate links, tool logins, "book now"), route them through a thin internal redirect page rather than linking out directly. (Reusable training pattern.)
- Artifacts: `webapp/src/app/remote/` (`page.tsx` + `RemoteRedirect.tsx`), `SiteHeader.tsx`; commit `5724a84`.

### 2026-06-24 — Reviews carousel + first on-site sign of the off-site treatment
- Phase·Step: Rebuild / social proof + AFTER signal
- What: Retitled the homepage testimonials to "What Our Clients Say" and replaced the static 3-up grid with a seamless auto-cycling carousel (arrows, 12s autoplay, pause-on-hover, reduced-motion aware). Added **two new 5-star Google reviews (Julie Visnich, William Swinney, both 2026-06-24)** — on-site review count **3 → 5**. Committed `20c6976`, deployed (noindex).
- Why: Better-presented social proof, and the new reviews are the **first visible product of the manual off-site treatment** Cotter started (review solicitation). Notably Julie's review describes pickup/return — corroborates the "Mobile Service" positioning.
- Decision(s): Display only, hand-curated — explicitly NOT auto-collection, so it does **not** contaminate the AI BEFORE baseline (per GM constraint). Considered + shelved a Google Places-API auto-pull (only ~5 reviews, Google picks them; manual curation gives better control at this volume).
- Lesson / adjustment: Review *display* and review *collection* are different things w.r.t. the baseline — displaying existing reviews is safe; soliciting/collecting is the treatment to time carefully. (Review timeline + dates tracked in the AIRI lane — cite, don't duplicate.)
- Artifacts: `webapp/src/site/components/TestimonialsCarousel.tsx`, `page.tsx`; commit `20c6976`.

### 2026-06-25 — 🚀 CUTOVER: rebuilt site goes LIVE on the production domain (the BEFORE→AFTER boundary)
- Phase·Step: Cutover / go-live — **the AFTER period begins**
- What: Cotter pointed `computerrepairdurango.com` DNS at DashNex Business + set up the `support@` domain mailbox; this session flipped the single switch **`SITE.indexable` false→true** (robots opens, sitemap activates) and deployed. The rebuild is now the live site. Bundled atomically into the cutover deploy: **GA4 `G-1Z9GV9J6MH`** (same property as the legacy site → continuous data), email everywhere → **support@computerrepairdurango.com** (display, schema, llms.txt, form recipient), LocalBusiness **`sameAs`** entity links (GBP / Facebook / Yelp / Bing Places), all Google-facing URLs absolute. **GSC verified by DNS TXT.** **Forms now DELIVER** (live test submission → `success:true` into support@). Commit `57871fb`, deployed. (Production HTTPS cert was still provisioning at cutover minute — normal; deployed build verified identical on the temp domain.)
- Why: This is the **on-site treatment going live** — the case study's BEFORE→AFTER boundary (GM-set ≈17:30 MDT, 2026-06-25).
- Decision(s): Single-switch cutover kept go-live to a one-line flip; everything bundled into one atomic deploy. Honest framing holds: the AFTER carries **5+ co-occurring confounders** (reviews, Bing Places, call uptick, Hiya flag, the cutover itself) → attribute nothing cleanly; expect **Layer-2 representation accuracy** to move sooner than **Layer-1 recommendation** (off-site, weeks-long AI lag). **n=1 proof-of-concept, not a universal law.**
- Lesson / adjustment: A pre-wired one-switch cutover (`SITE.indexable`) plus bundling GA + schema + email made go-live low-drama and atomic. Fresh-domain HTTPS cert provisioning is a normal few-minute delay, not a failure — verify the build on the temp domain meanwhile.
- Artifacts: commit `57871fb`; `config.ts`, `layout.tsx`, `schema.tsx`, `app/llms.txt/route.ts`. Tasks closed: cutover, GA4, **form email delivery**. BEFORE baseline locked in the AIRI lane; AFTER measurement starts now.

### 2026-06-25 — Post-cutover performance + accessibility polish (and a Stripe finding)
- Phase·Step: AFTER / quality hardening
- What: With the site live, ran PageSpeed + Agentic-Browsing audits and maxed every line in our control. **Final: Desktop Performance 81 · Mobile 55 · Accessibility 100 · Best Practices 100 · SEO 100 · Agentic Browsing 3/3 · CLS 0.** Fixes (each committed + deployed): star-rating `role="img"` (the Agentic-readability blocker), hero-kicker contrast → white, a visually-hidden `<h2>` to repair an h1→h3 heading-order jump, data-recovery hero 792KB→26KB WebP, the sitewide logo 78KB PNG→7.5KB WebP, explicit width/height on logo + hero (CLS→0), hero `fetchPriority`, and Google Fonts loaded non-render-blocking (~640ms). Commits `fbcfda7`→`6cc070a`.
- Why: **Agentic Browsing 3/3 was Cotter's stated top priority** for the AFTER — it's the layer that governs whether AI assistants can parse and correctly represent the business. The trio of 100s (A11y/BP/SEO) + CLS 0 are both UX wins and clean representation signals.
- Decision(s): Fix what we own; name honestly what we don't. **Mobile Performance (55) stays gated by DashNex framework JS/hydration** under Google's punishing mobile throttle — Desktop 81 (unthrottled) is the honest read of our own assets, now lean. The framework floor is deferred to a deliberate module-trim pass, not papered over.
- Lesson / adjustment: **The strongest training point of the whole build is a METHOD lesson, learned the hard way across several self-corrections.** Chasing the JS weight, I asserted in turn: "Stripe loads on every page" (from one PageSpeed line), then "Stripe loads on ZERO homepage chunks / checkout-only" (from a bulk curl-scan), then found **both were unreliable** — the bulk scan was silently corrupted by Cloudflare rate-limiting (empty responses grepped as "0"), and a local `dist/` build mis-sized chunks 100× vs deployed (an editor chunk read 545KB locally but 5KB deployed). **Only single, reliable fetches of the *deployed* chunks gave truth:** the homepage eagerly `modulepreload`s ~500KB+ gz of DashNex framework + all-modules code (`worker-entry` 257KB gz = react-dom+Lexical+Stripe-loader+Drizzle+framer; `theme-switcher` 142KB = framer-motion+Radix; etc.). The Stripe **loader** code *does* ship on every page as weight; whether the external Stripe.js *fetches* on content pages stayed honestly UNresolved (needs a real browser net-capture). **Levers, correctly separated: page-controllable (our `src/` — negligible) vs module-installable (removing unused modules — the only real reduction we own) vs platform-fixed (DashNex's eager bundling of a DB ORM + Stripe loader into the browser — their call).** Meta-lesson for the case study: **measure the deployed runtime with reliable instrumentation; never trust a single bulk scan, a stale local build, or one audit line — and state uncertainty as uncertainty.** (Training points.)
- Artifacts: `primitives.tsx`, `page.tsx`, `SiteHeader.tsx`, `layout.tsx`, `contact/page.tsx`, `api/contact/route.ts`, `public/site/{data-recovery,tcs-logo}.webp`; commits `fbcfda7`, `a4e5497`, `2bc575d`, `8c4928a`, `6cc070a`.

### 2026-06-25 — LCP/image-delivery perf round (the last in-our-control lever)
- Phase·Step: AFTER / performance
- What: With framework JS established as DashNex's floor (owner trims unused modules via the dashboard), targeted the remaining lever we own — **largest-contentful-paint and image delivery**, mobile's weak spot. Home hero is now **responsive** (`srcSet`: phones get a 17 KB / 768px image vs the 48 KB / 1920px one, −65%) and **preloaded** (`<link rel="preload" as="image">`, React-hoisted to `<head>`) so the LCP image is fetched before hydration rather than after. Blog post hero → `fetchPriority="high"` + explicit dimensions; 404 bg → dimensions. Audited our own client components — the only two (`SiteHeader` menu, `WhyWorkWithUs` tabs) are genuinely interactive, so our hand-written JS is minimal and justified. Commit `25505b6`, deployed + verified (had to force the correct IP through a recurring local DNS-cache flap).
- Why: It's the honest, demonstrable optimization left to us — the framework JS isn't ours to split, but image delivery and LCP discovery are. Strengthens the "DashNex + disciplined build = a fast SMB site" proof on the metrics we control.
- Decision(s): Optimize where we have authority (LCP/images), name the rest as platform floor; don't chase a single mobile number with risky changes. The real A/B (does module-trim move mobile?) is the owner's pending module-removal redeploy → re-measure.
- Lesson / adjustment: For SMB sites on a heavy platform, the owner-controllable performance wins cluster around **image delivery (responsive + modern format + preload + explicit dims)** and **module hygiene (remove what you don't use)** — the framework bundle itself is the platform's to optimize. (Training point: set client expectations on which levers are theirs vs the platform's.)
- Artifacts: `page.tsx`, `blog.tsx`, `not-found.tsx`, `public/site/hero-banner-{768,1280}.webp`; commit `25505b6`.

### 2026-06-27 — The site stops being a brochure: forms → CRM + a private client console
- Phase·Step: AFTER / turning the website into the business's operating system
- What: Wired both public forms into DashNex Contacts through a best-effort `saveToContacts` bridge (contact form → **lead**, check-in → **client**, auto-promoting an existing lead), capturing native address fields plus custom fields (computer type, reference, referral, details) and a `textable` tag mirroring the "OK to text" checkbox. Built a **private, owner-only TCS Clients admin page** on the DashNex module pattern (role-gated `admin`/`owner`, hard **403** to the public, sidebar entry via `system.menu_get`): sortable table, status labels (client / lead / no-fly), Text-OK / Address / AV columns, search. Added an **on-site vs drop-off/meet-up** selector to the check-in form. 🔒 The computer-login password is **never** written to Contacts.
- Why: An SMB site that only *collects* leads still leaves the owner doing data entry. Capturing straight into a CRM he already owns, plus a console he can actually read, converts the website from a marketing asset into the front door of the business.
- Decision(s): Build the console as a **native DashNex module page** rather than a bolt-on, so it inherits platform auth and the sidebar. Keep sensitive credentials out of the CRM entirely, even though the intake form legitimately needs them for the repair.
- Lesson / adjustment: The highest-leverage SMB web work is often **not** on the public pages, it is the owner-facing surface behind the login. Also: *capture what the business actually runs on, and refuse to store what it doesn't need.*
- Artifacts: `webapp/src/site/contacts.ts`, `webapp/src/site/admin/ClientsPage.tsx`, `/api/admin/clients`; full build + gotchas in `training-dashnex-contacts.md`.

### 2026-06-29 — One-way Google Contacts sync (and why the lower-fidelity store can never be master)
- Phase·Step: AFTER / operations integration
- What: DashNex Contacts now auto-push to **Google Contacts** (People API) in near-real-time, so every lead and client is instantly reachable from Google Voice and Gmail. OAuth refresh-token flow, search/create/update, an **additive** merge that never flattens Google's richer cards, tags → Google **labels**, and custom fields folded into the **Notes** block (Google's `userDefined` fields don't render reliably in its own UI). Server listeners on `contacts.contact_created/_updated/tag_added/tag_removed` plus a final sync at the end of `saveToContacts`, with an in-isolate email→resourceName cache to prevent duplicate creates. Verified live in both directions.
- Why: The owner lives in Google Voice and Gmail. A CRM record he can't reach from his phone is a record he won't use.
- Decision(s): **One-way, permanently.** A DashNex contact holds one email, one phone, one address, so it is structurally the *lower-fidelity* store and can never be Google's master. Deletes are deliberately **not** synced (additive rule: never destroy a record we may not have created).
- Lesson / adjustment: When integrating two systems, decide which one is **authoritative per field**, not per system, and let the lower-fidelity side stay a publisher rather than an owner. Sync direction is a data-model question, not a preference.
- Artifacts: `webapp/src/site/google-sync.ts`; 3 Google OAuth secrets via `dashnex secrets set`.

### 2026-06-30 → 2026-07-18 — The content engine: ~18 posts, real clusters, and a hard readability floor
- Phase·Step: AFTER / the AEO thesis executed at volume
- What: Shipped roughly **18 blog posts** in three weeks, each built from a **recorded owner interview** rather than invented copy: Windows 10 end-of-support, what repair really costs, cooling maintenance, why computers are slow, spotting online scams, surge protectors, tech overwhelm, SSD vs RAM, swollen batteries, on-site vs drop-off vs remote, is your data safe on drop-off, free antivirus isn't enough, the keyboard/toilet-seat piece, and more. Built a **blog↔service pairing template** (informational post funnels to the transactional service page) and ran a full **interlink cross-check across all 32 posts**: zero broken links, one-directional links cut 68 → 52. Added a **/blog search + topic filter** that keeps the entire post grid in the server HTML so crawlers and AI still see every card. Adopted a hard **6th-grade reading level** bar and the marketing-craft library (Seven Sweeps editing, ethical psychology levers, 40–60-word extractable answer blocks, X-vs-Y tables, cite stats with dates).
- Why: AI assistants and search both reward depth, structure, and genuine expertise. One-off posts don't move representation; **clusters** do, and clusters only work if the internal links actually reciprocate.
- Decision(s): **Interview the owner before writing, every time.** No post ships from imagination. Publish deliberately (WIP `published:false` → live-verify → flip), not on a content-calendar treadmill. Keep the filter client-side *on top of* fully server-rendered cards so interactivity costs nothing in crawlability.
- Lesson / adjustment: The moat for an SMB is the owner's **actual experience**, and the only way to get it into the page is to ask and transcribe. A generic listicle on the same topic ranks and cites worse than a plain-spoken one with a real bench story in it. (Training point: the interview is the product; the writing is assembly.)
- Artifacts: ~18 posts under `webapp/src/app/blog/`, `BlogFilter.tsx`, the reusable kit (TL;DR / Pro-Tip / FAQ-schema / hero / CTA / related / JSON-LD).

### 2026-07-07 — Service catalog consolidation (and retiring the catch-all)
- Phase·Step: AFTER / information architecture
- What: Grouped the six loose `/tech-services` line items into three real service pages (`ram-ssd-upgrades`, `os-reinstall-data-transfer`, `home-office-setup`), then **deleted `/tech-services`**, moved its "don't see it? call" catch-all to the `/services` hub, and repointed all 7 inbound links. Catalog is now ~14 pages plus a hub.
- Why: A catch-all page competes with its own children for intent and ranks for none of them. Every service a customer searches for deserves a page that answers exactly that search.
- Decision(s): Confirmed web-design and domain-registration belong to NLX, **not** TCS, so they never enter the catalog. A decision record, nothing to delete.
- Lesson / adjustment: Consolidation is not the same as deletion. Fold thin pages **upward** into stronger ones and always repoint the inbound links in the same commit, or you trade thin content for broken links.
- Artifacts: 3 new `webapp/src/app/services/*` pages; `/tech-services` removed.

### 2026-07-13 → 07-14 — Production incident: the D1 100-parameter cap, and shipping a diagnostic before a fix
- Phase·Step: AFTER / reliability, and the strongest engineering lesson in the build
- What: The owner's Clients feed began intermittently returning **500 with an empty body** once his client list crossed **100**. Cause: a raw `inArray(contactId, ids)` exceeded **Cloudflare D1's hard 100-bound-parameter-per-query limit** (deterministic, not the transient blip first suspected). Diagnosis was blocked because DashNex exposes no worker logs and a bare 500 carries no body, **so a diagnostic shipped first**: a self-creating `admin_error_log` table, the real error surfaced in `_dbg`, an owner-only `/api/admin/errors` viewer, and a read-retry. The logged SQL (101 `?` params) named the cause in one shot. Fix and prevention: `selectInChunks` (bulk `IN` reads chunked at 90) and `fetchAllPaged` (pages a list API to exhaustion), which also killed a latent 1,000-contact silent truncation. Swept the codebase; it was the only unbounded `IN`.
- Why: The failure only appeared **after** real-world growth, which is exactly when a small business is least able to tolerate it.
- Decision(s): **Instrument before fixing.** Resist the guess-and-patch loop when the platform gives you no logs. Fix the whole class (every bulk read), not the one query that broke.
- Lesson / adjustment: On serverless SQL, **a read must be safe at 10,000 rows the day you write it.** And when a 500 has no body, your first deploy should make it tell the truth, not attempt a cure. (Training point: observability is a prerequisite for debugging, not a luxury.)
- Artifacts: `webapp/src/site/db.ts`, `webapp/src/site/admin/error-log.ts`, `/api/admin/errors`.

### 2026-07-20 — Legacy ghost URLs: the thin-content problem nobody could see
- Phase·Step: AFTER / technical SEO cleanup
- What: Bing Webmaster Tools flagged pages for thin content and duplicate titles/descriptions. Root cause: the **old pre-rebuild flat URLs** (`/system-optimization`, `/avoid-data-loss`, `/tech-services`, `/tech-tips-news`, …) still resolved **200** through the DashNex catch-all into a generic shell, identical title and meta on every one. Ghost pages, invisible from inside the new site. Fixed with `redirects()` in `next.config.mjs` mapping every legacy URL to its new home; verified 308s live with all legitimate pages still 200. Later fired **IndexNow to Bing** after redirecting **34** legacy URLs.
- Why: A rebuild doesn't retire the old URL space. Search engines keep the old map long after the site changes, and a catch-all route will happily serve a thin page forever.
- Decision(s): Redirect rather than 404, preserving whatever equity the legacy URLs still carry.
- Lesson / adjustment: After any rebuild, **crawl the old URL list against the new site**, not just the new sitemap. Also confirmed: vinext honors `next.config` `redirects()`.
- Artifacts: `webapp/next.config.mjs`; Bing IndexNow submission.

### 2026-07-22 — The AI chatbot, and two platform landmines
- Phase·Step: AFTER / conversational layer
- What: Trained and shipped the **TCS Assistant** (InstraChat): crawled the site as source material, built a Q&A guardrail layer, and put a live bubble on the site. Guardrails verified against real customer-style questions: no street address, correct flat-rate pricing, and a hard refusal to accept passwords.
- Why: Visitors ask before they call. An assistant that answers in the owner's actual policies extends his voice past business hours.
- Decision(s): Use **only** the official DashNex module and widget, never hack module internals. Correct a premature "it's live" claim from the prior day once the bubble proved absent.
- Lesson / adjustment: Two landmines worth the whole entry. First, the publish switch was **hidden inside the agent's Integration (`</>`) tab** as a "Chat Bubble" toggle, entirely separate from the agent's "Active" status. Second, the module's `system.scripts_render` **auto-inject never reached hand-built app-router pages**, so a fully-configured agent rendered nothing, and the only way to see it was grepping the **live JS bundles** for the loader (zero hits) and pasting the widget snippet into the layout manually. *Verify the loader is in the shipped HTML, not just green in the dashboard.*
- Artifacts: `webapp/src/app/layout.tsx` (widget embed), `docs/instrachat-qa.txt`.

### 2026-07-25 — 📊 ONE-MONTH RESULTS: the before→after that the whole case study exists to produce
- Phase·Step: AFTER / measurement (the payoff)
- What: Compiled the full one-month report against the locked BEFORE snapshot. **Confirmed on-site gains:** Accessibility 77/82 → **100/100**; Agentic Browsing 2/3 → **3/3**; schema types 1 → **8**; blog posts ~17 → **32+**; service pages 6 → **14 + hub**; `/llms.txt`, GA4, and the Google Contacts CRM all live; legacy ghost pages redirected. **Organic signals (incognito):** a **#1 Google AI Overview** naming TCS and quoting the site's own copy, **#3 Perplexity**, **#3 Google organic**, and the local pack at **#4 with 10 reviews** (versus absent, with 3 reviews, at baseline). Grounded AI-visibility measurement put TCS **named in ~91%** of relevant prompts and **first in the room**, against a next-best competitor far behind.
- Why: This is the entire thesis under test: does disciplined on-site work plus honest, expert content move how **AI assistants** represent a small business.
- Decision(s): Measure only on the **grounded** path (live web-search-enabled queries), never the ungrounded base-model capture, which read a false 0 and hallucinated competitors that don't exist. Keep the confounders stated out loud rather than claiming clean attribution.
- Lesson / adjustment: **Grounding is the entire variable** in AI-visibility measurement. The identical model answers oppositely with and without live search, so an ungrounded scan measures training memory, not customer reality. And the honest framing holds: the AFTER window carries multiple co-occurring confounders (reviews, Bing Places, the cutover itself), so this is **n=1 proof-of-concept, not a universal law**.
- Artifacts: `tcs/docs/1-month-report-2026-07-25.md`; before/after comparison graphic.

### 2026-07-27 → 07-28 — Productizing: the Service Log and a tabbed admin module built for resale
- Phase·Step: AFTER / from one client's site to a resellable module
- What: Built a per-client **Service Log** (a real repair-ticket record: request date, machine, service requested, diagnosis, plan, services performed, service type, parts lifecycle, waiting-on, completion date, notes) that **never** syncs to Google. Then turned the Clients page into a **tabbed admin module**: Clients · **API Keys** · **Accounts**. The API Keys tab is a thin UI over the platform's own self-issued key endpoints (the security engine, hashing and validation and guards, stays untouched platform code), with a narrow grantable `service-log:write` scope so an outside AI agent can append tickets and do nothing else. The Accounts tab lists registered logins cross-referenced to the client list by email, the foundation for a client portal.
- Why: The owner recognized his own client console was the seed of something other small businesses would pay for, so it had to stop being TCS-specific.
- Decision(s): Keep every model **business-agnostic** (no hardcoded TCS copy; a different business edits one list of service types). Critically, split **internal vs client-visible at schema time**, before any client can log in: a client-visible `clientNote` field, an owner-only `notes` field for candid assessments, and a **default-deny allowlist** so a future portal can render only explicitly-permitted fields.
- Lesson / adjustment: **Build the privacy boundary into the schema before the audience exists.** Retrofitting a visibility split after customers can already log in is the expensive fix, and it usually surfaces as a customer reading something they never should have. Also: an allowlist stops fields nobody marked, it does **not** stop internal content typed into a marked field, so the field most likely to catch candid phrasing was deliberately kept off the client-visible list.
- Artifacts: `webapp/src/site/admin/service-log.ts`, `AdminModule.tsx`, `ApiKeysTab.tsx`, `AccountsTab.tsx`, `/api/admin/service-log`, `/api/admin/accounts`.

### 2026-07-29 → 07-30 — Platform lessons, and replacing a broken preview loop
- Phase·Step: AFTER / tooling and working method
- What: Ran down two DashNex platform behaviors that each cost real hours. **(1) `app deploy` re-ships the last *pushed* source, not the working tree**, so brand-new routes 404 through many clean local builds until `app push` runs first (the tell is a plain-text "Not Found" instead of the handler's JSON). **(2) `dashnex install` installs `dependencies` only and strips the entire `devDependencies` set**, which `dashnex dev` needs, so the dev server cannot start after a clean install (first `cross-env` is missing, then vinext's own `vite`). Restoring dev requires `npm install --legacy-peer-deps`, the flag being necessary because of an unrelated peer mismatch in the platform's own packages. Separately, replaced the local dev-server preview loop for content review with a **published live-preview page** built from the site's real design tokens: a stable URL, no server, readable on a phone, republished in place as edits land.
- Why: Content review was the highest-frequency loop in the project and the least reliable link in it.
- Decision(s): Escalate platform defects to the vendor with a clean reproduction rather than invasively working around them; fix our own code, report theirs. Keep the dev server as a fallback rather than a dependency, and reserve the real verification for the deployed URL.
- Lesson / adjustment: Two things. Technically: **know whether your deploy command builds from your disk or from the server's last upload**, because the failure mode is silent and looks like a code bug. Methodologically: when the client proposes a concrete, testable idea, **build the five-minute version before analyzing it**. Pre-emptive objections belong to irreversible actions, not cheap experiments, and a working artifact settles the question faster than the argument does.
- Artifacts: `tcs/docs/support/dashnex-devserver-devdeps-crossenv-bugcheck.txt`; published preview page.

### 2026-07-30 — Faithful adaptation: publishing the owner's own writing without rewriting him
- Phase·Step: AFTER / content method (a correction worth recording)
- What: Adapted the owner's own eight-year-old article, a ten-tip back-to-school list, into a live post. The first attempt **compressed his tips into punchy one-liners "for skim"**, merged some, and quietly **added a recommendation he had deliberately omitted** (biometric unlock, which he rejects on privacy grounds for children). He rejected it outright. Rebuilt as a straight transfer: his words, his structure, his ten numbered items, his original headline, with only branding stripped and links, components, and the affiliate placements wired in.
- Why: The article's value *is* his voice and his judgment. Twenty years of bench experience is the differentiator; smoothing it into generic web copy deletes the only thing competitors can't copy.
- Decision(s): When the source is the owner's own writing, the job is **faithful transfer, not editing**. Work from his actual text, never from a summary of it, because voice dies on the second paraphrase. Treat his omissions as **deliberate** until he says otherwise.
- Lesson / adjustment: "Skimmable" and "complete" are not opposites, a numbered list can carry a scannable heading **and** the full explanation underneath, and the depth is what earns the skim. The sharper lesson: **an omission can be a value judgment**, and filling it with a conventional best-practice puts words in the client's mouth that he would never say. (Training point: when adapting a client's own material, additions are riskier than deletions.)
- Artifacts: `webapp/src/app/blog/back-to-school-family-tech-checklist/page.tsx`; hero `back-to-school-family-tech-checklist.webp`.

### 2026-08-04 → 08-05 — The client portal goes live, and an AI seat writes to production for the first time
- Phase·Step: AFTER / the website becomes a system the customer logs into
- What: Shipped the **client repair portal** at `/my-repairs`. The owner chose a merge of two mockups he was shown: an **accordion** of every repair with the **progress track inside the expanded one**, active job open by default. In the same stretch, the **Tech Desk AI seat's scoped external API** went live (`GET /clients`, `GET|POST /service-log`, `PATCH /service-log/:id`) and was **proven end to end in production** — the agent-path seeded a real client's three-job history through a Bearer key, with enum validation, existence checks and duplicate-refusal all firing. Also killed an owner-facing data-loss bug and closed two schema gaps the Tech Desk found while mapping real jobs.
- Why: The Service Log only pays off when the customer can see it. "Where is my machine, and when will I hear from you" is the single most common question a repair business answers, and answering it once, permanently, in a place the customer can check themselves, is worth more than any page of marketing copy.
- Decision(s): **Enforce the visibility contract server-side, in one auditable file, before serialization.** The read path resolves the signed-in user to a client file by email, reads only that client's entries, and projects them through the `CLIENT_VISIBLE_FIELDS` default-deny allowlist *before the response is built* — so a tech-only field never enters the body at all and cannot leak through a forgetful component or a network tab. The progress track is derived **only** from allowlisted fields, so the UI cannot leak either. A **no-parts job renders no parts step**, which is only expressible because the Tech Desk pushed for `No parts needed` as an explicit value rather than a blank. No delete on the agent API, by design. `source` is forced to `agent` server-side so provenance can always be trusted.
- Lesson / adjustment: **Three lessons, and the third is the most transferable.** (1) *A safety check that fails in the field is worse than no check.* A modal backdrop click was silently discarding fully-typed service records; the first fix guarded it behind change-detection, which still failed for the owner and could not be reproduced. The answer was to delete the dependency on that check entirely — the backdrop now has no click handler at all — rather than re-tune a mechanism that had already proved untrustworthy. A redundant prompt costs a click; a silent discard costs the whole record. (2) *The platform's defaults are not your product's UX.* DashNex hardcodes the post-login redirect to an empty widget dashboard, with no config and no `?redirect=` support, which dropped a non-technical customer onto a blank screen. Overriding the route ourselves was the fix, but the general point is to walk the real first-run path as the least confident user, not as the admin. (3) *Design the operator's constraints into the schema, not into the operator.* The one field a client most wants ("what is this waiting on") was initially withheld because a free-text box invites internal phrasing that no allowlist can catch. Making it a **fixed picklist** removed the risk by construction and let the field ship — the constraint made the feature possible rather than blocking it.
- Artifacts: `webapp/src/site/portal/my-jobs.ts` (the single audit point), `webapp/src/site/portal/MyRepairs.tsx`, `webapp/src/app/my-repairs/page.tsx`, `webapp/src/app/dashboard/page.tsx`, `webapp/src/site/api/external-routes.ts`, `webapp/scripts/…`; commits `5ced220`, `053e3ad`, `3005a01`, `5f51bda`.

---
*Next milestones (not yet done): portal entry points (check-in thank-you CTA, invite flow) → status-change emails (native; DashNex has no SMS) → an append-only revision history so the record keeps superseded values instead of overwriting → continue the Data & Backup cluster → publish the case study.*
