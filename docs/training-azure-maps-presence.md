# Training Module: Add a "Where you're listed" tile with Azure Maps (and when to walk away from a vendor)

> **Status:** living capture. **Audience:** a non-coder wiring a third-party API into a DashNex app to add a real feature. **Slots toward Module 5 (Wiring real services) + Module 6 (the anti-bullshit rigor).** Captured live during the AIRI build, 2026-07-12, when Cotter added a directory-presence panel to the Xcavate report.

## What we were adding, and why
The Xcavate report already scored the business on **Google**. We wanted an easy-win **checklist of directory listings** ("have you claimed your Google / Apple / Bing / Yelp pages?") because unclaimed listings are cheap, real, actionable gaps. Google we already had for free (it's in the scrape). The question was which *other* directories are cheap enough to be worth a tile.

## ★ The first lesson: verify a vendor's price BEFORE you build on it
Yelp was the obvious add, its Fusion API even exposes a real `is_claimed` flag. So the plan said "add Yelp, it has a free tier." **It doesn't anymore.** Yelp's pricing page: **$229/month minimum** (their "Base" plan), billed on a 1,000-call-a-day floor, just to render a listing tile. The free tier is a 30-day trial and then it's gone.

**Cut it.** $229/mo for a presence checkmark is exactly the kind of vendor-tax that doesn't earn its keep. This is the same bullshit Cotter has paid for 20 years (four years of Yelp fees and not one customer ever said the word "Yelp").

**The teachable rule:** never quote a vendor's cost from memory or assumption. Open their pricing page and *read it*, or say plainly "I need to verify that" and attach no number. A confident wrong "it's free" steers a real build into a wall. (The AI running this build got this wrong twice in one evening — said Yelp was "a few dollars" — and Cotter caught it by pulling up the actual page. The catch **is** the method.)

So the real plan became: **Google (free) + Bing via Azure Maps (free tier) + Apple Maps (free with a dev account).** This module covers the Azure Maps piece.

## Signing up for Azure Maps (GNAT-standard walkthrough)

You need three things from Microsoft: an **Azure account**, a **free subscription**, and an **Azure Maps resource**, then you copy one **key**. Here's every screen, including the two that will try to stop you.

**1. Open a *fresh* Incognito / Private window, in Chrome or Safari, NOT Edge.**
So that Microsoft's leftover sign-in cookies from other Microsoft products don't hijack the login (that's the cause of the scary "silent sign-in" error below). A clean window sidesteps it.

**2. Go to the clickable link https://azure.microsoft.com/free and click the blue "Try Azure for free" button.**
Sign in with your Google/Gmail-based Microsoft account. So that Azure creates *your own* subscription and *your own* directory, this is the step that prevents the "wrong tenant" error below.

**Predicted fork — if a red error says "Selected user account does not exist in tenant 'Microsoft Services'":**
That means the portal tried to log you into a Microsoft-owned directory you're not part of, because you don't have your *own* subscription yet. Don't fight it on that screen. Close it, go back to **https://azure.microsoft.com/free**, and complete the "Try Azure for free" sign-up first. That creates your own "Default Directory," and the error disappears.

**Predicted fork — if a popup says "Interaction required / AADSTS50058 / silent sign-in":**
That's just stale cookies (its own text blames Edge). You're already in a fresh Incognito window from step 1, so if you see it, close the tab and reopen the link once. It clears.

**3. On the "Pick the plan" screen, choose the "Azure free account" card ($200 credit / "Try Azure for free"), NOT "Pay as you go."**
So that you get **"Spending protection, your credit card won't be charged."** If usage ever passed the free amount, Azure *stops* instead of billing you, there is no surprise-invoice path. (Pay-as-you-go puts a live card on the hook; we don't need that risk for a listing tile.) It asks for a card to verify identity; the free tier won't charge it.

**4. If a blue "Mandatory Azure MFA: Phase 2" popup appears, read it and close it. It's an FYI, not a task.**
So that you don't rabbit-hole: that notice only applies to *user-based* logins through Azure's command-line tools and SDKs. We authenticate with a **static Maps key**, not your user identity, so it does not affect anything we're doing. (Turning on MFA for your account later is good security, just not a blocker here.)

**5. On the Azure home ("Let's start building, Cotter"), click "Create a resource" (the green + in the top-left).**
Then in the search box **type "Azure Maps"**, click the **Azure Maps** result, and click the blue **Create** button. So that you provision the actual mapping service the tile calls.

**6. Fill the form:**
- **Subscription:** your free one (already selected). Leave it.
- **Resource group:** click **"Create new"**, name it `nlx`. So that all your Next Level resources live in one tidy folder.
- **Name:** `nlx-maps` (any name works). So that you can find it later.
- **Pricing tier:** choose **Gen2**, NOT Gen1. So that you're not on the tier Microsoft is retiring in September 2026.
- **Region:** pick any US one (e.g. East US). It doesn't affect the lookups.
- **Check the terms box.**
Then click **Review + create**, then **Create**. Wait about 30 seconds for "Your deployment is complete," then click **"Go to resource."**

**7. Get the key. In the resource's left menu, click "Authentication," then copy the "Primary Key."**
So that the app can prove it's allowed to make lookups. This one string is the whole prize.

**8. Do NOT paste the key into a chat or a file. Put it in the app's Admin → Xcavate → API tab, in the "Azure Maps key" field, and Save.**
So that it's stored **encrypted at rest** and never sits in plain text anywhere. (Same place all the other keys go, one settings home, secrets done right.)

## How it's wired into the DashNex app (the coder's half)
- **The key is added to one list** (`config/api-keys.ts` `EXTERNAL_KEYS` + a label/help entry). That list is the single source of truth, so adding a name there **auto-creates the admin form field** *and* the encrypted storage. No UI wiring by hand.
- **The lookup service is best-effort and NEVER throws** (`azure-maps.service.ts`). No key, a timeout, or a bad response all return a soft "unknown", a listing tile is never worth failing a customer's whole report over. (Same rule as the raw-scrape archive: peripheral I/O degrades, it doesn't crash the money path.)
- **Presence, not claim.** Azure Maps (like Bing) confirms the business *exists* in the map data ("Listed / Not found") but exposes **no "claimed" flag**, only Google and Yelp do. Be honest in the tile: don't print "claimed" where you can't verify it.
- **Results are cached** (30 days, per-provider, in the same R2 the scan already uses) so repeat report views don't re-spend the free quota.
- **Customers only see definitive tiles.** "Not set up yet" and "couldn't check" are operator states, filtered out of the customer report, so a half-configured panel never shows a client a broken-looking box.

## Gotchas for the Errors & Gotchas log
- **"Account does not exist in tenant 'Microsoft Services'"** = you have no Azure subscription of your own yet. Fix: complete the free sign-up first so you land in your own Default Directory.
- **AADSTS50058 "silent sign-in"** = stale cookies / Edge. Fix: fresh Incognito window, Chrome or Safari.
- **Pick "Azure free account," not Pay-as-you-go** — the free account carries **spending protection** (card can't be charged); PAYG does not.
- **MFA "Phase 2" popup is an FYI** — it targets CLI/SDK/MSAL *user* auth, not the static Maps key we use.
- **Pick Gen2 pricing tier** — Gen1 is retired Sept 2026.
- **Verify vendor pricing on the actual pricing page** — "free tier" ages badly (Yelp went from free to $229/mo). Never quote a cost you didn't just read.

## Reusable rule of thumb
Add the key name to one central list (it wires the admin field + encrypted storage for you), make the lookup best-effort so it can never break the report, be honest about what the data actually proves (presence ≠ claimed), and cache it. And before you ever build on a third-party API, **read its current pricing page with your own eyes**, the cheapest integration is the one you didn't overpay a vendor to include.
