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
- **What:** Decided to **inherit the BFT base app** rather than rebuild, and add a proprietary **deep AI-visibility upgrade** (the "zoom-in on AI" layer). Confirmed the engine live on GenX/Grok. Worked out strategy, funnel, competitive landscape, north-star; drafted the Deep-Dive spec + customer-question set.
- **Why:** the AI-visibility category is crowded and funded (peec, xfunnel, amionai, Scrunch, mangools) — AIRI wins on **segment + price + the Maps+AI bundle + the relationship**, not on out-featuring tools.
- **Decision(s):** inherit-don't-rebuild; AIRI = **14-day Deep Dive → Monitoring subscription**; the tool is the **front door, not the product** (fix = DFY + coaching); **honesty over fabricated gaps** (anti-Scrunch); decoupled + bring-your-own-wallet.
- **Lesson / adjustment:** Day 7 narrowed the "deeper report" moat (BFT already does AI citations + per-model + fixes) → the real moat is **time/distribution + the relationship**. Softened "blind loses / named flatters" into a **distribution** (single checks are noisy). Caught myself punching the *owner* instead of the *problem* in a report line (fixed).
- **Artifacts:** `nlxsystems/docs/{roadmap, airi-strategy, airi-deepdive-spec, scan-questions, phase-0-conclusion, fulfillment-model}.md`, `nlxsystems/docs/xd-bootcamp-notes/`.

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
