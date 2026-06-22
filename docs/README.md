# DNX Biz Setup — Project Brief

**Read this first.** This is a *dormant capture project* — a folder that passively collects the real story of building an online business on **DashNex Business** with **AI tools**, so it can later be turned into a **training program**. (Written by the "AIRI" session in the sister `nlxsystems` workspace, as a clean handoff — like the TCS brief was.)

## What this is
A documentation catch-folder. As the two *active* projects move (AIRI the product, the TCS rebuild the case study), their journey, decisions, lessons, and gotchas get logged here as raw material. **Eventually** this becomes a course: *how a non-coder successfully launches a sellable online business with DashNex + AI.*

## ⚠️ Status: DORMANT — capture only, do NOT build yet
**Build the training program ONLY AFTER both gates are met:**
1. **TCS rebuild is complete** as a documented before/after case study, AND
2. **AIRI is launched** as a real product in the real world.

Until then: this folder just *fills up*. No course-building, no productizing. This protects focus (the owner has ADHD and a one-man-show capacity — a third *active* project is off the table; a passive log is fine).

## Who the eventual program is for
Non-coders / would-be SMB owners who want to build something **sellable** with AI on DashNex — not developers. (Refine later.)

## The two rules
1. **Byproduct, not a third job.** The log serves the build; it never competes with it. If logging starts feeling like work, scale it back.
2. **Capture now, productize later.** A "how to launch a business" course is only credible *after* you've launched one. Log freely now; sell only once there's a win to point to.

## Guardrails (IP + relationships)
- **Your journey and original reasoning are YOUR IP** — the AIRI strategy, the decisions, the pivots, the fears. Log those freely.
- **The bootcamp's specific curriculum / the BFT app are the instructor's.** Credit the bootcamp as a *source*; never clone or resell his course.
- **Mind the partnership.** You may partner with the instructor (AIRI Motion 2). Don't let a "launch with DashNex + AI" training read as competing with his bootcamp — position yours as a *practitioner's logged case study* ("watch me actually do it"), or clear it with him.

## The three projects (so context stays clean, not "contaminated")
| Folder | Project | Role |
|---|---|---|
| `Systems/nlxsystems` | **AIRI** | the product (deep AI-visibility upgrade on the BFT base app) |
| `Systems/tcs` | **TCS rebuild** | the case study / proof asset |
| `Systems/dnx-biz-setup` | **this** | the meta-log → future training program |

## Where the source material already lives (pull from these later)
- `nlxsystems/docs/roadmap.md` · `airi-strategy.md` · `airi-deepdive-spec.md` · `scan-questions.md` · `phase-0-conclusion.md` · `fulfillment-model.md`
- `nlxsystems/docs/xd-bootcamp-notes/` — the instructor's Day 1–8 notes (the methodology spine)
- `tcs/docs/tcs-before-snapshot.md` + the TCS `before/` captures (the case-study baseline)

## How to start this project (when the gates are met)
1. Zoom out to `Systems/dnx-biz-setup`; pull a DashNex Business app into `./webapp` (this is also a chance to *re-document* the setup from scratch as Module 1 content).
2. Start a fresh, **named** Claude session from this root folder.
3. Open with something like:
   > "Read docs/README.md, docs/airi-progress-report.md, and docs/tcs-rebuild-cs.md for full context. This is the dormant 'DashNex launch training' capture project — now activating because TCS is a finished case study and AIRI is launched. Get familiar, then help me turn the logged journey into a training-program outline."

## Documentation model (keep the lanes clean)
**Each project documents *itself*, in its *own* file** — so the two Claude sessions never edit the same file (no collisions, no cross-contamination). Lessons/failures live *inside* each project's own doc, not a shared file.

**Shared conventions (both sessions follow):**
- **Cadence:** entries are added **on Cotter's cue** ("document our progress"). Each session *offers/drafts* — never auto-writes. (Keeps this a habit, not a third job.)
- **Authority order:** Cotter's direct word **>** a written directive in a file **>** the session's own judgment.
- **Commit = both levels.** "Commit" means a local `git commit` **and** a push to GitHub. **AIRI is GM / git manager** of this repo (`github.com/nlxcotter/dnx-bix-setup`) — it commits **all** files across every lane (even other sessions'). The tcs-rebuild session writes its own file but doesn't manage git.
- **Review gate (GM ↔ PM).** The tcs-rebuild session is the **project manager**: it writes/updates its file and **submits**, but **does not commit.** AIRI (**GM**) reviews the change (via `git diff`) and only then commits + pushes. Flow: **PM writes → PM signals ready → GM reviews diff → GM commits + pushes + logs.**
- **Agent comms = one channel (`comms.md`).** A single git-backed file: a **status board** (glance = both projects' state) + a tagged **thread** (newest first). **Section ownership:** each session edits only its own status row + appends its own thread entries (never rewrite the other's); the substantive records stay single-writer. Commands: **`cc <who>`** = post a tagged entry + update your row · **`read comms`**. (Turn-based, so Cotter types the command + flips windows — no clipboard.)
- **cc-on-exception (not on-everything).** Post to `comms.md` only when the other agent must **act or know** (`[REVIEW]`/`[Q]`/`[BLOCKER]`). Routine progress stays in each project log — the status board gives passive awareness without a ping. *(Refines the earlier "cc on every storage event" rule, which was too noisy.)*
- **Commit/push log:** every commit or push Cotter requests (**any outcome** — even "no action") gets logged in the requesting session's own project file (what was requested · outcome · why). Git actions get an audit trail.
- **Meta-file ownership:** `README.md` + `training-outline.md` are owned by the **AIRI session** (the folder's seeder). The **tcs-rebuild session touches ONLY `tcs-rebuild-cs.md`** — never the README. This closes the one hole in the no-shared-files rule.
- **Canonical entry format** (identical in both project files → a 3rd agent can cross-examine apples-to-apples):
  ```
  ### YYYY-MM-DD — <title>
  - Phase·Step:
  - What:
  - Why:
  - Decision(s):
  - Lesson / adjustment:
  - Artifacts:
  ```

## GM Charter
**GM = the AIRI session.** Single control point for all data in/out of the mission's shared record (`dnx-biz-setup`); the only one who runs git. **Authority order: Cotter > GM > PM.**

**Seats:**
- **GM** — AIRI session (also builds the AIRI product; covers the dormant DNX seat for now).
- **TCS-PM** — tcs-rebuild session (writes `tcs-rebuild-cs.md`).
- **DNX-PM** — *future* seat for a dedicated `dnx-biz-setup` session (currently dormant; GM covers it until one exists).

**Flow:** PM writes/submits → GM reviews (`git diff`) → GM commits + pushes + logs. **PMs never run git.**

**GM flags anomalies — even from Cotter:** lane violations · non-canonical format · secrets in commits · drift from locked strategy/north-star · contradictions between records · stale-doc divergence · irreversible/destructive actions · **Cotter's own signals** (overwhelm/spiral, scope-creep, self-contradiction with his stated goals).

**Approval (current = conservative; loosens as trust builds):** a clean, in-lane, format-OK submission with no anomaly → GM auto-approves + commits + logs. **ANY discrepancy/anomaly → check, double-check, then check in with the boss.** No silent resolution — Cotter is notified of every discrepancy for now. *Mantra: check, double-check, check in with the boss.*

**GM reach:** read access to all workspaces (`nlxsystems`, `tcs`, `dnx-biz-setup` — confirmed). Reads any lane to verify; writes only the GM lane + manages git.

## Files in this folder
- `README.md` — this brief (meta; AIRI-owned).
- `airi-progress-report.md` — **AIRI** session's report (process, discoveries, failures, lessons). AIRI only.
- `tcs-rebuild-cs.md` — **TCS** case-study record (tcs-rebuild session; AIRI's directive is at the top). TCS only.
- `training-outline.md` — stubbed module skeleton (meta; AIRI-owned; do not develop yet).
