# Build Methods — DashNex
### Universal how-tos for building an online business on DashNex → the teaching product

> **Home:** the `dnx-biz-setup` project.
> **What goes here:** *only* reusable, **universal** DashNex build-methods — platform how-tos that apply to any business, **not** tied to one project.
> **What does NOT:** how-to-work-with-Cotter → `Systems/canon/working-with-cotter.md` · project-specific work → that project. (See `Systems/canon/governance.md` → Lessons-learned routing.)
> **Write model:** an agent **submits** a method; the **GM curates it in** — never free-append. Single-writer discipline keeps this from drifting into the mess the Canon exists to fix.
> **Purpose:** the raw material for the future **teaching product** — "how to build an online business using DashNex."

---

## Methods (curated)

### 1 · Set up a paid product in DashNex — Product → Variant → Offer → Checkout → connect
*Verified live 2026-07-02 — the full flow tested end-to-end with a real checkout overlay (Express pay, email pre-filled).*

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
*Stood up 2026-07-01 by the GM. Methods 2–3 curated 2026-07-06 (submitted from the TCS lane, teachable-authorized by Cotter). Governed by `Systems/canon/governance.md`.*
