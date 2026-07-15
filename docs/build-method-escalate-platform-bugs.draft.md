# DRAFT SUBMISSION → build-methods.md

> **Status:** Submitted by TCS-PM 2026-07-14, **teachable-authorized by Cotter. Classified: PM-TEACHABLE** (build knowledge for a new project manager, NOT owner-facing course content). For GM to curate into `build-methods.md` (single-writer), and to confirm the PM classification with Cotter per the new PM-vs-Client split. Born from a real incident: a DashNex dev-server crash I first tried to self-fix by proposing to downgrade the owner's machine Node. Generalized, no secrets.

---

### N · Know your lane: escalate DashNex PLATFORM bugs to support (with a clean bugcheck), fix only your own code
*DashNex docs are light and you have zero access to their server code, so trying to self-fix a platform-level bug is guessing, and guessing plus invasive action breaks more than it fixes. This tells you where the line is and what to hand support.*

**What it does & why it matters.** On DashNex you own your app code (`src/`), but you **cannot see the platform's server code**, and the public docs are thin. So for any failure in THEIR layer (the dev server, the CLI, Node/undici crashes, module internals, the deploy pipeline), you are working blind. The disciplined move is to **fix your own code, and escalate their platform with a clean bugcheck report**, rather than reverse-engineer something you cannot inspect. It saves hours, and it avoids the bigger sin: making invasive changes to the OWNER'S machine (their Node version, global installs) on a guess.

**The rule (draw the line first):**
- **YOUR code** (a query, a page, a route, a repo config) = yours to fix. Example: a `WHERE IN` query hitting D1's 100-parameter cap is your bug; fix it.
- **THEIR platform** (dev server crashes, CLI errors, Node/runtime incompatibility, module `dist` internals, deploy internals) = escalate to DashNex support. Do NOT self-fix.
- **When you cannot tell which, treat it as theirs and escalate.** Cost of wrongly escalating is a support ticket; cost of wrongly self-fixing is a broken environment and lost trust.
- **Never modify the owner's system unilaterally** (Node version, global npm, package manager). If a system change is the fix, let support say so, then the OWNER decides. It is their machine.

**The bugcheck report (what support actually needs):** enough to reproduce and diagnose without a back-and-forth.
1. **Versions:** DashNex CLI version AND its declared `engines`; Node version AND how it was installed (Homebrew, nvm, etc.); OS + arch; the dependency at the crash site and its version (e.g. `undici`).
2. **Symptom:** one plain sentence, plus what still works (e.g. "dev server serves pages then the process exits; `dashnex check` passes and production is unaffected").
3. **Verbatim crash:** the exact stack, ANSI stripped, not paraphrased.
4. **Repro steps:** minimal, and note if it repeats across clean restarts.
5. **Your analysis:** what you can infer without their source (e.g. "response body begins with gzip magic `1f 8b` but reaches `JSON.parse` undecompressed; crash is inside undici, not our code").
6. **Pointed questions:** things only they can answer (e.g. "which Node LTS is certified; is Node 26 known-incompatible; should `engines` have an upper bound?").

**⚠️ Landmines:**
- **Do not declare a platform bug "fixed" without proof.** A crash that recurs after a "fix" costs more trust than the original bug. If it is intermittent, say intermittent.
- **Distinguish a bug YOU introduced from a platform incompatibility.** If you corrupted `node_modules` with a bad install, own it and rebuild cleanly (see the dev-preview recovery recipe). If the platform is incompatible with the environment (e.g. a too-new Node major), that is an escalation, not a hack.
- **Newer is not always better.** A bleeding-edge Node major installed silently by a package manager can break a CLI whose `engines` floor (`>=22`) technically allows it but was only tested on the LTS. "Mysteriously broke" is often a silent runtime bump.
- **The bugcheck report is a legitimate deliverable, not a failure.** Handing the owner a clean, forwardable report IS the win when the problem is theirs.

**The rule (one line):** on DashNex, fix what you can see (your code), escalate what you can't (their platform), and never backtrack the owner's system on a guess.

---
*Submitted 2026-07-14 by TCS-PM, teachable-authorized by Cotter, classified PM-Teachable. Pairs with the dev-server-preview build-method (both about DashNex platform boundaries). GM to curate into `build-methods.md`; retire this `.draft.md` once folded in.*
