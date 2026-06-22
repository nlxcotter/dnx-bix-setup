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
