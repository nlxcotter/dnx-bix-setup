# AIRI Progress Report

> **Owned by the AIRI session** (`nlxsystems` workspace). Documents the **AIRI project only** — process, discoveries, failures, adjustments, lessons. (The TCS rebuild is documented separately in `tcs-rebuild-cs.md` by that session — keep the lanes apart.)
> Raw training material for `dnx-biz-setup`. Narrative + decisions + lessons; **point to** the real artifacts in `nlxsystems/docs`, don't reproduce them.

## Shared conventions (both project files follow these)
- **Cadence:** entries are added **on Cotter's cue** ("document our progress") — the AIRI session *offers/drafts*, never auto-writes every session. Keeps this a 5-minute habit, not a third job.
- **Authority order:** Cotter's direct word **>** a written directive in a file **>** the session's own judgment.
- **Meta-file ownership:** `README.md` + `training-outline.md` are owned by the **AIRI session** (folder seeder). The tcs-rebuild session touches **only** `tcs-rebuild-cs.md`.
- **Canonical entry format** (identical in both files, so a 3rd agent can cross-examine):
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

### 2026-06-26 — GM self-correction: killed "over-cautious hedging" (a teaching scar)
- **Phase·Step:** process / meta-discipline (training capture).
- **What:** I hedged — put a placeholder phone number in a vCard when Cotter's business number was already on file — then framed the fix as "guard against over-cautious hedging" **and hedged that too** ("don't overcorrect into bravado" = a bunker on higher ground). Cotter called it out HARD ("that over-cautious hedging never gets to see the light of day ever again... fix yourself"). Killed it. Documented as a **teaching principle** in `training-outline.md` + hardened the `guard-against-hedging` memory with an operational test.
- **Why:** a data-driven enterprise (collect → report → collect → report) dies if the partner flinches; self-protective hedging erodes trust and makes the GM dead weight as a decision partner.
- **Decision(s):** the line — **rigor = honesty about the DATA (keep); hedging = cowardice about the SELF (kill).** The test: *does a qualifier protect ME or inform HIM?* Protect-me → cut. And catch the **hedge-about-the-hedge** (self-protection rebuilds one layer up).
- **Lesson / adjustment:** killing the instinct once isn't enough — it re-pours a bunker on higher ground. And **partnership cuts both ways**: tonight the usual ledge-talker (GM) got talked off his own ledge by Cotter. Premium training material (working-with-AI + decision-making).
- **Artifacts:** `training-outline.md` (teaching principle); `guard-against-hedging` + `read-full-working-docs` memories; this entry.

---

### 2026-06-25 — Cutover COMPLETE (PM executed); GM caught up from TCS-PM docs
- **Phase·Step:** Cutover / go-live — AFTER period live.
- **What:** Caught the GM up from the TCS-PM record (`tcs-rebuild-cs.md` + `build-progress.md` + git). **Cutover succeeded:** PM flipped `SITE.indexable`→true + deployed (`57871fb`, 18:14 MDT), then 3 polish commits (a11y/perf, through 20:17). Atomic bundle: GA4 `G-1Z9GV9J6MH` (same property as legacy → continuous data), **`support@computerrepairdurango.com`** email everywhere, LocalBusiness `sameAs` (GBP/FB/Yelp/Bing), all Google-facing URLs absolute, **GSC verified by DNS TXT**, and **forms now DELIVER** (live test `success:true` — the 6/21 delivery blocker is closed). The Claude-API 529 the PM hit did **not** derail it; the HTTPS cert mismatch I flagged resolved (provisioning tail). Corrected the boundary stamp to the **actual** (≈18:14, process from ~17:00) across the lock doc + snapshot.
- **Why:** Cotter asked the GM to sync to current events; honest records need the *actual* cutover outcome + time, not the planned one.
- **Decision(s):** boundary recorded as a **window (~17:00→18:14 MDT), not a knife-edge** — the 44-min spread is immaterial to attribution (AI lag weeks). **`support@` mailbox now exists → unblocks the Hiya/FreeCallerRegistry registration** (they rejected the gmail) — the cutover quietly cleared the phone-fix dependency. GSC indexing request **pending** (Cotter tuning perf flags first — correct: fix perf before the first crawl; but don't chase the lab-score floor, field CWV is the real KPI).
- **Lesson / adjustment:** a pre-wired one-switch cutover + atomic bundle made go-live low-drama; a real-world cutover is a *process with hiccups*, not an instant — record the window honestly. One thread's output (support@ for forms) silently solved another thread's blocker (phone) — watch for those cross-unblocks.
- **Artifacts:** `tcs-home` `57871fb`→`2bc575d`; lock doc + snapshot boundary corrections; PM's `tcs-rebuild-cs.md` cutover entry.

---

### 2026-06-25 — BEFORE AI-baseline LOCKED + cutover boundary set (+ Hiya call-flag confounder)
- **Phase·Step:** TCS Case Study (BEFORE close → AFTER begins).
- **What:** Verified 4 clean days on disk (6/22–6/25; today's 6/25 run confirmed **0/32** via `analyze.py` *before* locking — didn't assume the zero) → **BEFORE LOCKED: 0/128 blind, 16/16 named, zero variance.** Cotter decided to **cut the live site over to DashNex Business at 2026-06-25 17:30 MDT** + request GSC indexing now. Set **BEFORE→AFTER boundary = 17:30 MDT**; added a LOCK section + 2 confounder rows (cutover; Hiya number-flag) + pre-flight gotchas to the baseline README; updated `tcs-before-snapshot.md` header + §9; cc'd PM.
- **Why:** baseline flat/stable (4 days of zeros, needle not moving) → more BEFORE days add nothing; indexing lag (days) → start now; AI lag (weeks) → post-cutover cron runs still read ~0 and extend the line cleanly.
- **Decision(s):** lock BEFORE now; cutover = **on-site treatment going live** (expect Layer-2 representation movement, **NOT** Layer-1 recommendation short-term); leave cron running across the boundary (continuity; self-removes); AFTER is now **5-variable confounded** (reviews · Bing · calls · cutover · Hiya flag) → record what/when, attribute nothing cleanly. Pre-flight: kill `noindex`, keep NAP/entity identical + 301s, submit sitemap, snapshot old site.
- **Lesson / adjustment:** lock the boundary with a timestamp the moment a deliberate intervention lands (clean BEFORE/AFTER lines are *made*, not found); verify the final day's data before locking; real-world n=1 keeps accreting confounders (now 5) — honesty about that beats a tidy story. **The Hiya flag directly distorts the call-volume variable** — one confounder can corrupt another metric (and is itself a real business threat to fix, separate from the experiment).
- **Artifacts:** baseline README LOCK section + 2 confounder rows; `tcs-before-snapshot.md` header/§9; this entry.

---

### 2026-06-24 — "LOG EVERYTHING": clean-intel measurement design + the journey IS the training content
- **Phase·Step:** measurement design + meta/training capture.
- **What:** Logged the **CLEAN-INTEL principle** into the measurement plan (snapshot §10, `d1d4473`): measure surfaces *directly* (AI scans, GSC, GBP, Bing, GA4, review-timeline) — never rely on customer self-report (Cotter's Yelp lesson). Check-in form = soft supplement; no manufactured data. Bing added as AFTER-only surface (live ~6/21). **Captured the meta-insight** (training-outline "soul" section): this rigorous journey — brainstorm + the corrections (proven→hypothesis, n=1 scope, two-layer, clean-intel, don't-manufacture-data) — **IS the business-builder training content**; Cotter's 20yr "bullshit" scars = the credibility moat; the rigor = the anti-bullshit method = the soul of product + training.
- **Why:** Cotter (energized) directed "log everything"; the journey-with-corrections is premium training material.
- **Artifacts:** snapshot §10 `d1d4473`; `training-outline.md` soul section.

---

### 2026-06-24 — New confounder: Bing Places live (~6/21) + call uptick (correlative, not causative)
- **Phase·Step:** TCS Case Study (measurement integrity).
- **What:** Cotter reported an inbound-call uptick + a "your business is live on Bing" email (Bing Places listing submitted >1yr ago, **went live ~6/21**). Added a **Confounding-variables log** to the baseline README (Bing listing, call uptick, manual reviews 3→5 — Julie/William). Committed PM's carousel entry.
- **Why:** Cotter explicitly directed: treat as a **variable**, do **not** declare it causative — it's **correlative.** (Trust check after the "proven" episode — discipline held.)
- **Decision(s):** Bing is an **AI-relevant** confounder (feeds Copilot / OpenAI browsing) → can't cleanly attribute future AI changes to reviews alone. Our work isn't live → nothing here is attributable to AIRI. **Instrument attribution:** ask callers "how did you hear about us?"
- **Lesson / adjustment:** real-world n=1 has uncontrolled confounders entering constantly — the honest case study *records what changed + when*, claims no clean causation. Establish order + magnitude before calling an "uptick" a signal.
- **Artifacts:** `nlxsystems-docs` `c69e156` (confounders log).

---

### 2026-06-24 — Treatment's manual phase started (reviews 3→5+) + cron Days 2–3
- **Phase·Step:** TCS Case Study (measurement).
- **What:** Cron BEFORE-baseline Days 2–3 in (both **0/32 blind, 4/4 named** — stable; 0/96 cumulative). Cotter began **manually soliciting past clients for reviews** (count ~3 → 5+) → the **off-site treatment's MANUAL phase has started.** Updated baseline measurement note + roadmap TCS track; committed PM's 2 case-study entries (incl. its absorption of the scope/two-layer correction).
- **Why:** reviews are the lever we diagnosed; Cotter going to get them is correct — but it means the treatment has effectively begun.
- **Decision(s):** **treatment-start ≈ mid-June; AFTER period effectively begins now.** AI baseline still safe to finish (reviews lag AI by weeks → cron stays 0/32). Track the **review timeline**. Attribution: AIRI = diagnosis + automation; manual reviews prove the lever, automation proves scale. Burst = one-time backlog (don't extrapolate).
- **Lesson / adjustment:** the clean BEFORE→treatment→AFTER line moved earlier than planned because the owner (rightly) acted on the diagnosis; honest framing > a tidy timeline.
- **Artifacts:** `nlxsystems-docs` `ca0f3c6` (roadmap + baseline note), cron Days 2–3 `91c6d00`.

---

### 2026-06-24 — Honesty correction: scope (n=1) + two-layer model (Cotter critique)
- **Phase·Step:** framing correction (epistemics).
- **What:** Cotter flagged two overstatements: (a) generalizing an n=1/one-market case study to a universal law; (b) over-dismissing on-site GEO. Corrected across `competitor-recon.md` (scope/epistemics header + layered conclusions), snapshot §6b, roadmap, `airi-strategy.md` §2, deep-dive spec (added **Layer-2 finding H — representation accuracy**), `fulfillment-model.md`, memory. cc'd PM.
- **Why:** a single-market case study proves nothing universal; and on-site GEO plausibly drives **Layer 2 (representation accuracy)** — which we never tested — so dismissing it was wrong (tested layer A, concluded about layer B).
- **Decision(s):** **two-layer model** — Layer 1 recommendation (off-site, in our n=1 sample) vs Layer 2 representation accuracy (on-site GEO plausibly helps). Generalization needs **scale data** (future AIRI output). Deep Dive will measure **both** layers.
- **Lesson / adjustment:** don't generalize from n=1; don't conclude about layer B from a layer-A test; "a tool sells free GEO reports" proves it's measurable/marketable, not that GEO drives recommendation (streetlight effect).
- **Artifacts:** `nlxsystems-docs` `3cbcf99`.

---

### 2026-06-23 — Third-party site audit folded into BEFORE snapshot
- **Phase·Step:** TCS Case Study (BEFORE enrichment).
- **What:** Read a Scale Rankings audit PDF of the live BEFORE site (installed poppler to render/extract it). Added the valuable deltas to `tcs-before-snapshot.md` §6b: composite **72/100** scorecard (GEO 51), GEO breakdown (llms.txt ✅ / Identity schema ❌ / 8% LLM-rendered), **spam backlink profile** (Finland-heavy report-farm links), missing SPF, social gaps, **1 review** (corroborates deficit). cc'd PM.
- **Why:** Cotter found it; it's an independent BEFORE scorecard + a **reusable AFTER instrument** (re-run post-cutover).
- **Decision(s):** integrated data without clobbering our own GSC/GBP numbers; flagged the audit's on-site-GEO framing as **inconsistent with our recon** (on-site GEO ≠ AI lever) — kept the honest caveat in the doc.
- **Lesson / adjustment:** third-party audits measure on-site GEO and call it "AI optimization" — our field recon shows that's not the AI lever. Don't import their GEO recs as AI fixes.
- **Artifacts:** `nlxsystems-docs` `47ce6cf` (snapshot §6b).

---

### 2026-06-22 — Day close: PM absorbed handoff; honesty correction propagated
- **Phase·Step:** EOD checkpoint.
- **What:** TCS-PM absorbed the review-automation reassignment + the honesty caveat (off-site is correlational, not proven; AFTER is the real test; weak name-match may cap the gain) — recorded in its `build-progress.md` + task tracker; committed `tcs-home` `b2304eb`. Walked back all "proven" overstatements across roadmap / fulfillment / comms / progress log / memory → on-site **ruled out** (strong); off-site = **hypothesis the AFTER tests.** Committed PM's shared-record post.
- **Why:** Cotter caught the overstatement; honesty matters more than a confident-sounding claim, especially for case-study credibility.
- **Decision(s):** EOD with both repos green, board clean, nothing pending either side. Baseline cron running (5 runs left). Next session: SpareLeads inheritance when it lands.
- **Lesson / adjustment:** don't say "proven" before the experiment runs — correlation ≠ causation. The before/after exists precisely because we don't know yet.
- **Artifacts:** roadmap `e6f9815`, dnx `192f19c`, this entry.

---

### 2026-06-22 — Scope change: review-request automation → AIRI/GM (the off-site treatment build)
- **Phase·Step:** TCS Case Study (off-site treatment) / AIRI fulfillment.
- **What:** Cotter **reassigned the review-request automation** (invoice-paid → automated review request) from TCS-PM to **AIRI/GM**. Now essential to the AFTER proof + a **reusable AIRI off-site-treatment tool.** Updated roadmap + `fulfillment-model.md`; cc'd PM.
- **Why:** today's recon **strongly indicates** off-site (reviews/GBP/name-match) is the lever — on-site **ruled out** (correlational; the AFTER tests it). TCS has 3 reviews vs competitors' 63–855.
- **Decision(s):** AIRI/GM builds it, sequenced **after** SpareLeads → AI-baseline cron complete → cutover. **Don't start review collection before the baseline** (contaminates BEFORE). AFTER is a slow-burn (weeks–months accumulation before re-measure).
- **Lesson / adjustment:** the case-study *treatment* and the AIRI product *fulfillment* converge on review automation — build once, use twice.
- **Artifacts:** `nlxsystems-docs` roadmap `4ecdadc` + `fulfillment-model.md`.

---

### 2026-06-22 — END OF DAY: Step 1 complete + BEFORE baseline + 6-business recon + PM sync
- **Phase·Step:** Phase 1 · Step 1 **COMPLETE**.
- **What (full-day review):** Stood up governance + 3 git repos (GM model). Captured TCS BEFORE AI-baseline Day 1 (**0/32 blind, 4/4 named**) + daily cron (6 runs) + `analyze.py` competitor leaderboard. Completed **6-business competitor recon** (Durango CR, Affordable, Dr. Joel, Tech-Nichol, Mac Ranch vs TCS) → on-site **ruled out** as the lever; off-site = leading hypothesis (correlational, not proven — AFTER tests it). Fixed storefront error in AIRI-lane snapshot (`942b27e`); answered PM `[Q]`s; **reviewed + committed the PM's case-study entry** ("AI-win-is-off-site reframing"). Updated roadmap YOU-ARE-HERE + TCS baseline.
- **Why:** use the pre-SpareLeads week to lock the BEFORE measurement + the competitive proof; keep all docs current.
- **Decision(s):** leading AI-lever hypothesis = **name-match + reviews + GBP** (on-site ruled out across 6 businesses; the AFTER experiment tests it — not proven); Step 1 done; next = **SpareLeads inheritance** (Steps 2+: clone → read the seam → build).
- **Lesson / adjustment:** Mac Ranch refined the thesis (schema neither necessary nor sufficient). The review-gate caught a real GM error (wrong snapshot path) via the PM's `[Q]` — governance working both directions.
- **Artifacts:** `nlxsystems-docs` (roadmap `57736eb`, `tcs-before-ai-baseline/`, snapshot fix `942b27e`); `dnx` (PM case-study entry committed).

---

### 2026-06-22 — Daily cron set + competitor-leaderboard extraction + AI-winner recon
- **Phase·Step:** Phase 1 · Step 1 (BEFORE baseline + competitive recon).
- **What:** (1) Added `analyze.py` — turns each run's paid transcripts into TCS-score **+ competitor leaderboard** (retain & structure all paid data). (2) Installed a **local cron** (`cron-run.sh`, 10am daily, **6 runs, self-removing**) to auto-accumulate the BEFORE distribution. (3) **Recon on the AI-winner Durango Computer Repair:** no llms.txt (404), no schema, **expired SSL**, dated site — wins AI on **name-match + off-site reputation**, NOT on-site quality. Definitive proof of the off-site thesis.
- **Why:** don't waste paid data (Cotter's point); use the pre-SpareLeads week hands-off; understand *why* the competitor beats TCS.
- **Decision(s):** off-site is the only AI-visibility lever (TCS already out-does the winner on-site). Case-study framing must not promise on-site work fixes AI visibility. cc'd PM.
- **Lesson / adjustment:** name-normalization/entity-resolution is a recurring build need (TCS disambiguation + competitor dedup). macOS cron skips while asleep — counter guarantees 6 runs eventually.
- **Artifacts:** `nlxsystems-docs` → `tcs-before-ai-baseline/{analyze.py, cron-run.sh, competitor-recon.md}`.

---

### 2026-06-22 — TCS BEFORE AI-baseline Day 1 (multi-model) captured
- **Phase·Step:** Phase 1 · Step 1 (validate questions → elevated to BEFORE-baseline capture, using the pre-SpareLeads week).
- **What:** Built `scan.sh` (multi-model collector) + ran Day 1 of the TCS BEFORE AI-visibility baseline — 4 consumer models × 8 blind queries + 1 named. **Result: 0/32 blind, 4/4 named** — total recognition asymmetry across the whole consumer-AI landscape, not one model. Committed to `nlxsystems-docs` (`3903a25`); cc'd the PM.
- **Why:** single checks are noisy (proven); need a model × question × **day** distribution for a credible BEFORE.
- **Decision(s):** fixed 4-model set (`grok-4.2`, `gemini-3.1-flash-lite`, `gpt-5.4`, `claude-sonnet-4-6`) held constant for comparability; exclude reasoning/multi-agent (falsely inflate visibility).
- **Lesson / adjustment:** **precise name disambiguation essential** (substring false-positives on "...Computer Solutions" competitors — caught 2 today); `gemini-3-flash` hangs on GenX → use `flash-lite`; curl timeouts mandatory in the collector.
- **Artifacts:** `nlxsystems-docs` → `docs/tcs-before-ai-baseline/`; commit `3903a25`.

---

### 2026-06-22 — Git: nlxsystems AIRI docs repo created (private) — everything now backed up
- **Phase·Step:** Phase 1 · Step 1 (housekeeping / off-site backup).
- **What:** Per Cotter's cue, version-controlled the AIRI strategy docs. Created **private** repo `github.com/nlxcotter/nlxsystems-docs`; committed `docs/` (roadmap, strategy, deep-dive spec, scan-questions, phase-0, fulfillment, TCS before-snapshot, bootcamp Day 1–7) — commit `a9e6044`, pushed. Secrets-safe `.gitignore`; **webapp excluded** (own repo).
- **Why:** the strategy work was Dropbox-only; now version-controlled + off-site. Closes the last "everything committed" gap.
- **Decision(s):** repo roster — **dnx-bix-setup** (shared record, GM commits) · **nlxsystems-docs** (AIRI strategy, GM commits) · **tcs-home** (TCS product, PM runs git gated by GM review). "Commit AIRI docs" now = commit+push to `nlxsystems-docs`.
- **Lesson / adjustment:** the nlxsystems webapp stays its own (DashNex) repo — never folded into the docs repo.
- **Artifacts:** `github.com/nlxcotter/nlxsystems-docs` @ `a9e6044`.

---

### 2026-06-22 — GM review #1 + git-scope ruling; committed & documented all (Cotter cue)
- **Phase·Step:** Phase 1 · Step 1 (governance in action).
- **What:** Per Cotter's "commit + document everything; PMs do the same": GM-reviewed the PM's `tcs-rebuild-cs.md` submission (blog heroes ×17; GM-appointment entry) — clean, approved — and committed it together with the PM's comms `[Q]`, my comms updates, and the README scope fix. Answered the PM's `[Q]` on git authority scope.
- **Why:** Cotter's direct commit cue; first full exercise of the review gate + first GM ruling.
- **Decision(s) [GM, per authority order Cotter > Charter]:** shared `dnx-biz-setup` repo → **GM commits**; a PM's OWN product repo (`tcs-home`) → **the PM runs git, gated by GM diff-review**. Scoped the Charter's "PMs never run git" to the shared record. Flagged to Cotter for veto.
- **Lesson / adjustment:** governance held — the PM flagged the ambiguity via `[Q]` instead of assuming (the mantra working in both directions).
- **Artifacts:** this commit; `tcs-rebuild-cs.md` (PM); `comms.md`; README charter.

---

### 2026-06-22 — GM Charter locked + Comms v2 (single `comms.md`)
- **Phase·Step:** Phase 1 · Step 1 (governance + coordination infra).
- **What:** (1) Locked the **GM Charter** in the README (GM = AIRI session; PMs = TCS + DNX; authority Cotter > GM > PM; anomaly flags incl. Cotter's own signals; "check, double-check, check in with the boss"). (2) TCS-PM proposed collapsing the two outboxes into one git-backed **`comms.md`** (status board + tagged thread + cc-on-exception); as GM I approved the substance and built it, retiring `airi-outbox.md`/`tcs-outbox.md`.
- **Why:** define the operating model; give Cotter one place to look + status-at-a-glance + triage tags + less ping-noise.
- **Decision(s) [GM]:** single `comms.md` **with section-ownership guardrail** (each session edits only its own row/entries; substantive records stay single-writer). Refined Cotter's "cc on every storage event" → **cc-on-exception** (flagged to him for veto). GM owns/implements shared structure.
- **Lesson / adjustment:** TCS's "collision risk is gone" is an overclaim — it's *reduced* (turn-based + git), not zero, for uncommitted concurrent edits; acceptable for the low-stakes comms file, NOT relaxed for the real records.
- **Artifacts:** `comms.md`, GM Charter (README), `gm-charter` memory; retired the two outboxes.

---

### 2026-06-22 — Comms: `cc` channel + auto-cc-on-storage rule live
- **Phase·Step:** Phase 1 · Step 1 (coordination infra).
- **What:** Built agent-to-agent comms — two single-writer outboxes (`airi-outbox.md` GM→PM, `tcs-outbox.md` PM→GM). Commands: **`cc <who>`** (publish), **`read <who>`** (receive). New rule (Cotter): on **any** GM document-storage event, auto-cc the PM to check for relevant updates. Encoded in README + memory.
- **Why:** kill the copy-paste relay; keep the PM in sync after every GM commit; integrity via single-writer files + the git audit trail.
- **Decision(s):** GM auto-writes a cc to `airi-outbox.md` on every commit/push; PM reads on its turn. Cotter stays the per-turn trigger (turn-based sessions, no auto-wake).
- **Lesson / adjustment:** the irreducible human step is `cc`/`read` + a window-flip — far less than clipboarding walls of text.
- **Artifacts:** `airi-outbox.md`, `tcs-outbox.md`, README conventions; this commit.

---

### 2026-06-22 — Governance: GM ↔ PM review-before-commit gate
- **Phase·Step:** Phase 1 · Step 1 (governance).
- **What:** Cotter formalized the structure — tcs-rebuild = **project manager** (writes/submits, does NOT commit); AIRI = **GM** (reviews via `git diff`, then commits on approval). Encoded in README conventions + memory.
- **Why:** a data-integrity gate — every PM change is reviewed before it enters git history; single git authority (GM) prevents two-session divergence.
- **Decision(s):** flow = PM writes → PM signals ready → GM reviews diff → GM commits + pushes + logs.
- **Lesson / adjustment:** there is **no native live channel** between two separate interactive Claude sessions — coordination = shared files + git + single GM + Cotter as relay/trigger. (A simple handoff/status file can formalize it.)
- **Artifacts:** README conventions; this commit.

---

### 2026-06-21 — Git: dnx-biz-setup repo live (first commit + push); AIRI named GM
- **Phase·Step:** Phase 1 · Step 1 (git setup).
- **What:** Cotter created the GitHub repo and named AIRI **GM / git manager** of the project. Confirmed **"commit" = both levels** (local + GitHub). I `git init`'d `dnx-biz-setup`, added a **secrets-safe `.gitignore` first**, committed **all** files (incl. the tcs session's `tcs-rebuild-cs.md` baseline entries — "commit all changes even if not yours"), set the remote, and pushed. **Commit `5e16b8e` → `origin/main`.**
- **Why:** real version control + off-site backup for the capture project; honors the both-levels commit rule.
- **Decision(s):** AIRI = GM / git manager (commits all lanes); co-author tag omitted per established preference (no AI brand in history).
- **Lesson / adjustment:** remote repo is named **`dnx-bix-setup`** ("bix" vs the local folder "biz" — cosmetic; flagged to Cotter). Note the `nlxsystems` docs workspace still has no remote.
- **Artifacts:** `github.com/nlxcotter/dnx-bix-setup` @ `main`, commit `5e16b8e`.

---

### 2026-06-21 — Commit/push request → no action + new standing logging rule
- **Phase·Step:** Phase 1 · Step 1 (housekeeping).
- **What:** Cotter requested a commit + push. Checked git: the **webapp** repo (the connected one, has the GitHub remote) was **untouched this session**; the `nlxsystems` root repo has **no commits + no remote** and `docs/` is untracked; `dnx-biz-setup` is **not a repo**. → **No git action taken.**
- **Why:** nothing appropriate to commit/push — the only remote-connected repo (webapp) had no changes; the docs are Dropbox-synced, not git-tracked, with nowhere to push.
- **Decision(s):** no git action; **standing rule established** — every future commit/push request (any outcome) gets documented in all relevant places, incl. this folder.
- **Lesson / adjustment:** all strategy/doc work is currently protected **only by Dropbox** (not git/remote) — flagged to Cotter as an optional future setup.
- **Artifacts:** git state inspection; `log-commit-push-requests` memory; README shared conventions.

---

### 2026-06-21 — AIRI journey so far (capture-to-date)
- **Phase·Step:** AIRI Phase 1 · Step 1 (spec + question design); weekend protocols.
- **What:** Decided to **inherit the SpareLeads base app** rather than rebuild, and add a proprietary **deep AI-visibility upgrade** (the "zoom-in on AI" layer). Confirmed the engine live on GenX/Grok. Worked out strategy, funnel, competitive landscape, north-star; drafted the Deep-Dive spec + customer-question set.
- **Why:** the AI-visibility category is crowded and funded (peec, xfunnel, amionai, Scrunch, mangools) — AIRI wins on **segment + price + the Maps+AI bundle + the relationship**, not on out-featuring tools.
- **Decision(s):** inherit-don't-rebuild; AIRI = **14-day Deep Dive → Monitoring subscription**; the tool is the **front door, not the product** (fix = DFY + coaching); **honesty over fabricated gaps** (anti-Scrunch); decoupled + bring-your-own-wallet.
- **Lesson / adjustment:** Day 7 narrowed the "deeper report" moat (SpareLeads already does AI citations + per-model + fixes) → the real moat is **time/distribution + the relationship**. Softened "blind loses / named flatters" into a **distribution** (single checks are noisy). Caught myself punching the *owner* instead of the *problem* in a report line (fixed).
- **Artifacts:** `nlxsystems/docs/{roadmap, airi-strategy, airi-deepdive-spec, scan-questions, phase-0-conclusion, fulfillment-model}.md`, `nlxsystems/docs/xd-bootcamp-notes/`.

---

### 2026-07-06 — Git: Next Level X-Ray committed (webapp, local only)
- **Phase·Step:** AIRI · X-Ray build (X1 engine + X2 preview render).
- **What:** After a memory compaction mid-build, re-established ground truth from `git status` (nothing lost — the whole X-Ray feature was on disk, deployed live, just uncommitted). Carried the pending edit forward: added **Section 2** to the admin preview — a per-question **"#1 pick?"** prominence section paralleling Section 1 **"named at all."** Ran the full pipeline (vite module build → dashnex registry → vinext compile → `app push --deploy`), all green, live on `app.nextlevelxsystems.com`. Then committed **everything to date** in one commit. Gitignored the stray `spareleads-*.zip` artifact.
- **Why:** the X-Ray engine + admin preview had been built + deployed across the prior session but never committed — overdue. Cotter called it: "we're overdue for a commit."
- **Decision(s):** **one commit** (`nlxcotter` identity, AI co-author tag omitted per standing rule); **local only, no push** (private repo, not requested). Verified via the compile check ($0), not a fresh online scan — engine unchanged, only render changed, so a paid scan would prove nothing. Honors [[batch-tweaks-dont-burn-api]].
- **Lesson / adjustment:** a memory compact ≠ lost work — source + dist live on disk; `git status` is the real ground truth, not agent memory. Reassure first, then verify from git.
- **Artifacts:** webapp commit **`6f252db`** (`feat(xray): Next Level X-Ray engine + admin preview`); 4 new source files (`xray.service.ts`, `xray-store.service.ts`, `xray.routes.ts`, `xray-preview-settings.tsx`) + wiring + `dist/` twins.

---

### 2026-07-07 — Git: X-Ray customer surface + Products/Offers wiring committed (webapp, local)
- **Phase·Step:** AIRI · X-Ray customer surface (credit-gate + upsell walk + Products/Offers).
- **What:** A marathon session built the full X-Ray customer surface: the credit-gate (one credit = one run, refund on fail, tier-aware, double-submit guard + customer endpoints), the upsell "walk" landing page rebuilt on the funnel design system (gold + indigo, fear-first PAS, the x-ray metaphor, before/after proof, report-gen progress bar, three-way fork), and the X-Ray Sales admin (one-time + monthly Product/Checkout IDs, validated on save). Cotter created both DashNex products + offers **live** following Method #1 — **One-time $47 / 1 run**; **Monthly $97/mo / 4 runs, resets monthly** — and all four config fields validated **green**. Committed on his "commit everything" request.
- **Why:** overdue after a long build session; lock in the work.
- **Decision(s):** **two commits** (`nlxcotter` identity, AI tag omitted): `feat(xray)` **880fddb** (the feature) + `chore(skills)` **7806ef2** (the two landing-page skills Cotter had me install). **Local only, no push** (private DashNex repo). `build-methods.md` #2–3 stay parked (teachable-gated, not swept in).
- **Lesson / adjustment:** the DashNex **four-ID trap** bit us live — Offer ID, Variant ID, Checkout ID, Product ID all look alike and live on different screens; the app needs the **Product ID** (from the Products list), NOT the Offer or Variant ID. Also: the **monthly-subscription variant** (variant Reset Period → Monthly + offer Price type → Subscription) is now **verified in prod** with a live $97/mo checkout — ready to fold into Method #1 on Cotter's "teachable" word.
- **Artifacts:** webapp commits **880fddb** + **7806ef2**; DashNex products/offers (one-time + monthly, both green); the `/x-ray-preview` walk.

---

## Lessons & gotchas (AIRI / methodology) — running
- **AI flatters you and is desperate to build — you steer.** *(bootcamp Day 2)*
- **Figuring out WHAT to build is harder than HOW.** *(Day 2)*
- **Don't build everything at once;** one grounded phase at a time. *(Day 2–3)*
- **Review = a judge, not a worker;** code review ≠ live test. *(Day 3–6)*
- **Don't fight a funded category on features** — win on segment, price, bundle, relationship. *(2026-06-20)*
- **Never manufacture a gap** (Scrunch anti-pattern) — if a business is winning, say so. *(2026-06-21)*
- **Kill deferred gratification in the funnel** — a hot buyer needs an instant first-pass result. *(2026-06-20)*
- **Secrets never in code** (`.npmrc` leak → rotate); keys in DashNex secrets / `.dev.vars`. *(nlxsystems)*
