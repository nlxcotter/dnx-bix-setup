# DRAFT SUBMISSION → build-methods.md

> **Status:** Submitted by TCS-PM 2026-07-06, **teachable-authorized by Cotter.** For GM to curate into `build-methods.md` (single-writer). Verified-in-prod across the TCS blog (20+ live posts). Generalized here, no project specifics or secrets. Point to Canon `gnat-standard.md`, do not copy it.

---

### N · Build an effective blog article for a local-business site (the whole method)
*Verified live across the TCS rebuild, 20+ published posts, 2026. This is the repeatable system, not theory.*

**What it does & why it matters.** A local-business blog is not "content for content's sake." Each post is a **customer's real question answered honestly**, which does three jobs at once: it ranks in search, it feeds AI answer engines (via schema + `llms.txt`), and it earns trust so the reader calls *you*. Done loosely, posts read like filler and convert nobody. Done with a system, each one is a small, reusable asset. This is that system, end to end: how to pick and shape a post, how to write it, and how to ship it.

**The process, in order:**

**1. Queue an idea, don't free-write.** Keep an ideas folder, one file per topic. Each idea file names: the **exact customer question** it answers, the search intent, which **services it drives**, and why now (seasonal? recurring call?). This keeps the blog aimed at revenue and questions, not the writer's whims.

**2. Interview the owner BEFORE writing.** The single biggest quality lever. Ask 3-5 targeted questions and build the post on the answers, not on generic web research. The owner's bench truth (real prices, real war stories, the misconceptions they see daily, what they'd *never* recommend) is what makes a post un-fakeable and correct. *So that* the post carries authority no competitor can copy. **⚠️ Trap 1 — never publish web-research claims the owner hasn't confirmed.** On TCS a "generally reliable" claim from articles was flat wrong per the owner's 20 years; his correction became the post's strongest section.

**3. Draft as unpublished (WIP).** Write the post but keep it `published: false` (or your platform's equivalent) so the page renders at its URL for preview but stays out of the index, sitemap, and `llms.txt` until it's ready. Preview on a local dev server **only when the owner asks**, and shut that server down as part of shipping.

**4. Ship deliberately.** typecheck → deploy → **verify live** (curl the URL + hero + confirm it's in the index) → commit/push → shut the dev server down. If `llms.txt` and the sitemap are generated from the published-post list (they should be), the post surfaces in both automatically on publish, no manual step.

**The craft standards (what makes the writing land):**

- **Title = search intent, not clever.** Title it the way a worried person would type or say the question ("Why won't my laptop charge?"), not a cutesy headline. Keep a separate display H1 if you want line breaks.
- **Reusable component kit, built once, reused every post:** a **TL;DR box** (skimmable bullets up top), a **Pro Tip callout** (boxed, for the one insight most people miss), an **FAQ block wired to FAQ schema** (4-5 real questions), a **hero image** (consistent size, e.g. 1200x675 WebP), a **call-to-action**, **related-reading links**, and **article + FAQ JSON-LD**. Assemble, don't re-style per post.
- **GNAT-Standard reading level.** Write at a **6th-grade reading level or under** (see Canon `gnat-standard.md`). Short sentences, plain words, active voice. Every unavoidable technical term gets **defined on the spot or cut** ("joule = a number on the box; higher means it soaks up more power"). **⚠️ Trap 2 — jargon is invisible to the writer.** Words like "boilerplate," "clamp," "consumable" feel normal to write and lose the reader instantly. Read the finished post as the least-technical customer you have.
- **Voice: warm, plainspoken, honestly expert.** Speak as the owner ("in 20 years on the bench..."). **Earn the emotional beat where it genuinely fits** (validating a scared reader's feelings lands harder than any feature list), but don't force it. State real prices and real "you only pay when it's solved"-type honesty. Match the house style (e.g. no em-dashes if that's the rule).
- **Safety and liability.** When a topic touches physical safety (electrical, fire), be emphatic and route readers to a licensed pro rather than DIY. Include the real manufacturer warnings verbatim where they exist.
- **Internal links** to related posts and the relevant service pages, every post feeds the next and the money pages.

**The GBP (Google Business Profile) companion — do this for every post:**
- Write a **condensed version** for a Google Business Profile post the same day.
- **⚠️ Trap 3 — GBP posts must contain ZERO contact info** (no phone, URL, or email in the body) or Google auto-rejects them. Put the link only in the "Learn more" button.
- Keep it under ~1,500 characters, lead with the hook, use restrained emoji ("pepper, not frosting"), and point "Learn more" at the live post URL (schedule it for after the post goes live so the button never 404s).

**The traps, one line each — the hour-savers:**
1. **Interview the owner first;** never ship web-research claims they haven't confirmed.
2. **Jargon is invisible to the writer** — enforce a 6th-grade read, define or cut every term.
3. **GBP posts allow no contact info in the body** (link goes in the button) or Google rejects them.
4. **Keep it WIP (`published:false`) until shipped;** verify live + confirm it's in `llms.txt`/sitemap after deploy.
5. **Preview server is on-request only,** and shutting it down is a ship step.

---
*Submitted 2026-07-06 by TCS-PM, teachable-authorized by Cotter. GM to curate into `build-methods.md`.*
