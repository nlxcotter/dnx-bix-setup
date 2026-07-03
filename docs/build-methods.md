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
*Stood up 2026-07-01 by the GM. Governed by `Systems/canon/governance.md`.*
