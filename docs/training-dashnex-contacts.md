# Training Module: DashNex Contacts, end to end (forms in, data model, sync out to Google)

> **📒 SCOPE NOTE:** This doc was originally "Wiring Custom Forms into DashNex Contacts." It was broadened on **2026-06-28** to cover the whole Contacts surface: the data model's hard limits, and syncing Contacts **outward** to Google (Gmail / Google Voice). Forms-in is Part A (unchanged, verified); the data model and outbound sync are Parts B and C.

> **🎓 TEACHING NOTE (Cotter, 2026-06-27) — the foundational principle behind this whole doc:** On DashNex you have **limited control over the provided/prebuilt modules** (you cannot edit their UI or internals; they regenerate on build). But you have **full autonomy over your own custom code** — new pages, routes, and admin tools you build and gate yourself in the Admin Dashboard / app. **Strategy: lean on the provided modules for their data and services (contacts, emails, automations, inbox), and build your own UI and logic on top.** Do not fight the prebuilt screens; build your own alongside them, reading the same shared data so the native modules keep working. *(Proven this session: a private owner-only `/api/admin/clients` feed that returns a hard 403 to the public while reading the same contacts the native modules use.)*

> **Author:** TCS PM session. **Status:** living. **Audience:** anyone building a custom-coded DashNex Business app who wants their own forms to feed the built-in CRM (Contacts), with custom fields, lead/client labeling, and an outbound sync to Google Contacts.
>
> **The one-line lesson (forms-in):** On a *no-code* DashNex builder (PowerTech Site Builder), an Easy Opt-In form is the *only* bridge from a form into Contacts. On a *coded* DashNex app, you are not bound by that. You can call the contacts service directly, which keeps your own forms, validation, and notification emails intact. Use the built-in pieces (custom fields, tags, contact type) from the cleaner side: server code.

---

# PART A — Forms into Contacts (verified)

## 1. The goal
Two public forms on the site (Contact and Check-in) currently only send an email to support@. We want each submission to also become a DashNex **Contact**, with:
- the Contact page tagged **lead**, the Check-in page tagged **client**;
- structured check-in detail stored as **custom fields**;
- an existing **lead** automatically becoming a **client** when they submit a check-in.

## 2. How DashNex Contacts is actually built (what the module gives you)
From `@dashnex/contacts`:

- **Contact record (fixed fields):** firstName, lastName, email (required), phone, address1/2, city, state, zip, country, lat/long, timeZone, image, ip, **notes** (free text), **emailsAllowed** (bool), and a first-class **`type` enum of `lead` | `customer`**. The base record has NO arbitrary custom-field column.
- **Custom fields (separate system):** dashboard-managed under Contacts -> Custom Fields. Types: Text, Text Area, Number, Date, Dropdown, Checkbox. Definitions live in a `contactFieldDefinitions` store; values live in `contactFieldValues` (keyed to contact id + field definition id). They are NOT part of the contact's base schema, which is why a code-only peek at the contact schema misses them. (Lesson: the dashboard is the source of truth for what features exist; do not conclude "not supported" from the base model alone.)
- **Tags (separate model):** create/assign freely. Service methods: `getTagByName`, `addTagsToContact`, `removeTagsFromContact`, `getContactTags`.
- **Opt-in forms:** a full lead-capture widget system (`getOptinEmbedCode(form, 'react' | 'script')`) plus a public submit endpoint (`/api/contacts/optin/...`). The public endpoint is protected by a **signed token** (`generateToken` / `verifyToken`), a **honeypot**, and **rate limiting** minted by the official embed. It is built to be used as DashNex's own form widget, not fed from a third-party form.

### Contact service API (server-side, `getContacts()`)
The methods that matter for this job:
`createContact(input)`, `getContactByEmail(email)`, `updateContact(contact, input)`, `getContactById`, `listContactsByType('lead'|'customer')`, `createTag`, `getTagByName`, `addTagsToContact`, `removeTagsFromContact`, `getContactTags`. Custom field values are written separately into `contactFieldValues` (no inline `fieldValues` on `createContact`).

## 3. The decision: opt-in widget vs code-direct (and why)
| | Native opt-in widget | Code-direct via contacts service (CHOSEN) |
|---|---|---|
| Keeps our custom form UI/validation | No (renders DashNex's widget) | Yes |
| Keeps our support@ email format | No / risky | Yes |
| Risk of subscriber double-opt-in email | Yes | No |
| Requires reverse-engineering token security to POST from our form | Yes | No |
| Uses native custom fields / tags / type | Yes | Yes |
| Who manages fields + tags | Dashboard | Dashboard (same) |
| Where the wiring lives | Dashboard mapping | 2 server routes (owned by dev) |

**Chosen: code-direct.** We use all the built-in CRM pieces, written from our own routes. The opt-in widget would be a downgrade in UX and control for zero gain here. The opt-in-required rule was a *no-code-builder* constraint, not a DashNex law.

## 4. Implementation pattern (resilient upsert)
For each form route, AFTER the existing email send, do a best-effort contacts write:
1. `getContactByEmail(email)` -> find existing.
2. If none, `createContact({ firstName, lastName, email, phone, type })`. If found, `updateContact(...)`.
3. Resolve the tag by name (`getTagByName('lead' | 'client')`) and `addTagsToContact`. For the check-in (client) path on an existing lead: `removeTagsFromContact(leadTag)` + `addTagsToContact(clientTag)` + set `type: 'customer'`. That IS the "lead becomes client" automation, in code, no automation engine.
4. Write custom field values into `contactFieldValues` by looking up each field definition by label (the labels the owner created in the dashboard must match the code).
5. Wrap the whole contacts write in try/catch. **A contacts failure must never block the email or the visitor's success screen.** Email + UX are the contract; CRM is additive.

## 5. Non-negotiable security rule
The Check-in form collects the customer's **computer login password**. It must **NEVER** be written to the CRM/contacts (it would persist sensitive credentials in a marketing database). It stays only in the transient email to support@. Exclude it explicitly from every contacts write. (Same family of rule as: no street address, service-area only.)

## 6. Owner vs dev split (the reusable playbook)
- **Owner (dashboard, low-code):** create the custom fields (exact labels), create the tags (lead, client). That's it. Everything else is dashboard-managed going forward (viewing contacts, filtering by tag/type, exporting).
- **Dev (code):** the bridge in the two routes (upsert + type + tags + field values + best-effort). Field labels in code must match the dashboard labels exactly.

## 7. Gotchas captured for the Errors & Gotchas log
- Custom fields exist in the dashboard but are invisible in the contact's base zod schema (separate store). Don't conclude a feature is missing from the code model alone.
- The public opt-in endpoint requires the embed's signed token + honeypot + rate limit, so you cannot cleanly POST to it from a foreign form. If you want the native widget, you take its UI too.
- `createContact` does not accept inline custom-field values; they go to `contactFieldValues` separately, keyed by field-definition id (look up by label).
- Make contacts writes best-effort so the CRM never becomes a single point of failure for the user-facing form.

## 8. VERIFIED implementation + every gotcha hit (2026-06-27)
Shipped and proven live end to end: contact upsert by email, native `type` (lead on contact form, customer on check-in, promoted on a later check-in), tags (lead/client, lead removed on promotion), and custom field values all landing on the contact. **Opt-in forms were NOT required** (the owner suspected they were; disproven by test).

**Gotchas, in the order they bit us:**
1. **Phone format.** DashNex's contact schema rejects a formatted phone like `(970) 555-1234` ("must start with an optional + followed by digits"). `createContact` threw, the best-effort catch swallowed it, and **no contact was created at all**. Fix: strip phone to digits before the CRM write (`replace(/\D/g, '')`). The site form keeps its pretty format. *Lesson: one bad field rejects the whole contact; normalize before writing.*
2. **Custom fields must be pre-created in the dashboard.** Code cannot create field *definitions*; only the dashboard UI can (Contacts -> Custom Fields -> Add Field). Until they exist, value writes are silently skipped. The "Opt-in form placeholder" on that dialog is **optional helper text, not a requirement** — leave it blank, no opt-in form needed.
3. **Match by Label, read the Key.** Definitions have a display `Label` (e.g. "Computer Type") and an auto-generated lowercase `key` (`computer_type`). Code matches the dashboard **Label** (exact, case-sensitive) and reads whatever key that field has. So the key text never matters; only the Label must match the code.
4. **Field VALUES write to `contactFieldValues`** (`contactId`, `fieldKey`, `value`, composite PK) via `getDatabase()` + the exported drizzle models. No high-level service setter exists. Delete-then-insert per (contactId, fieldKey) to upsert.
5. **The contacts LIST columns are hardcoded** (Name, Email, Type, Phone, Created). Custom fields only render on the contact **detail** page (an "Additional Information" section). You CANNOT add custom-field columns. So: anything you need to *slice/filter the list by* is a **tag** (tags are filterable) or the **type** (lead/customer, filterable); descriptive detail is a **custom field**; anything you need the instant a job lands is already in the **email**.
6. **Two label layers, by design.** Native `type` is a fixed enum (`lead`/`customer`, cannot be renamed); tags are owner-named (`lead`/`client`). Set both: type powers the built-in list filter, tags carry the owner's wording + extra filtering.
7. **Diagnose silent best-effort failures by temporarily surfacing the error/state through the API response**, then revert. That is how both the phone bug and the "zero field definitions" state were found in one test each, instead of guessing.

**TCS field map (final, verified):** native name/email/phone + type; tags lead/client; custom fields `Computer Type` <- make/model, `Reference` <- referral source, `Referral` <- referral detail, `Details` <- issue (all Text). Computer login password and contact-page subject/message stay email-only.

---

# PART B — The Contact data model's HARD limits (this drives everything downstream)

A DashNex contact holds exactly **one** of each: one `email` (**required + UNIQUE** across the workspace — `email` is `notNull().unique` in `@dashnex/contacts/dist/models/contact.model.js`), one `phone`, one address block, one `notes` field, one `type`. There is **NO multi-value support**: you cannot store a second email or a second phone on a single contact. Tags and custom-field values are the only "many" you get, and they hang off that one record.

Consequences that shape every later decision:
- **Email is the identity key.** Upserts, dedup, and any external sync must match on email. **No email = the record cannot exist** (import silently skips it; in one real Google export ~30 of ~140 contacts had no email and were dropped).
- **A richer external store cannot be mirrored INTO DashNex without loss.** Google contacts hold multiple emails/phones and hand-built "couple" cards (e.g. a single combined "Jim & Sheri" contact carrying two people's numbers). DashNex physically cannot represent that. **Therefore DashNex is always the LOWER-fidelity store** when paired with Google. This is the single fact that forces the sync direction in Part C.
- **Importing a Google CSV:** standard Google export has clean structured address columns (Street / City / Region / Postal) that map to address1/city/state/zip, so a contact's address can drive downstream "has address" logic. But watch: phones arrive in mixed formats and sometimes wrapped in invisible Unicode marks; multi-value cells may be jammed with a ` ::: ` separator; and the no-email rows drop. Test-import 2-3 rows (one with a full address, one without) before committing the whole file.

---

# PART C — Syncing Contacts OUT to Google (one-way, additive)

Decided for TCS on 2026-06-28, reusable for any DashNex business that lives in Gmail / Google Voice. Wave (invoicing) was deliberately left OUT of sync (its public API is restricted/deprecated; owner keeps Wave separate).

## C1. Direction: ONE-WAY, DashNex → Google. Never two-way.
The rationale falls straight out of Part B: DashNex cannot hold Google's multi-value richness or the owner's couple cards, so **it must never be the master that overwrites Google.** Two-way also reopens the dedup/merge swamp (which-version-wins conflicts; no-email rows Google would try to push that DashNex rejects; Google can't push in real time anyway). One-way out keeps DashNex as a clean source of truth and Google as the richer downstream.

## C2. Trigger: real-time via events, not polling.
`@dashnex/contacts` emits on the core event bus: `contacts.contact_created`, `contacts.contact_updated`, `contacts.contact_deleted` (plus `contacts.tag_added/removed/created/updated/deleted`). Register a listener in your `_app` module (same mechanism as a `system.menu_get` listener) and push to Google on each event — that is true real-time, not a poll. DashNex ALSO exposes a full public REST contacts API (generate **API keys** under dashboard Settings; auto-generated **OpenAPI** docs; scoped roles like `contacts:read` / `contacts:write`) if you prefer to drive sync from an external worker, but the in-app event listener is the simplest real-time path.

## C3. Match + write: STRICT email match, ADDITIVE writes.
- **Find** the Google contact by email (People API `people:searchContacts`). If found, **UPDATE**; if not, **CREATE**. Do NOT rely on Google's own "merge duplicates" suggestions — prevent the duplicate at write time instead.
- **ADDITIVE is the whole safety model:** update only the fields DashNex owns (name, the Client/Lead label, fill a blank) and **NEVER wholesale-overwrite the Google record.** A naive "replace Google's contact with DashNex's version" would flatten Google's extra phones/emails and the owner's hand-built couple cards. Touch only what you own; leave everything else intact. *One line: DashNex feeds Google, Google is allowed to be richer, and the sync respects that.*
- **STRICT email-only dedup** (accept a rare manual duplicate when emails don't line up) beats fuzzy phone/name matching (which risks wrongly fusing two different people — the "two Kathys / two Mikes" problem). Default strict unless the owner explicitly trades it away. (Owner confirmed he has never wrong-merged in Google, so a stray dup is a 5-second manual fix; a wrong merge is the nightmare.)

## C4. Auth / setup (the only real friction)
A one-time Google Cloud OAuth setup: create a project, enable the **People API**, approve OAuth consent, store a **refresh token** as a DashNex secret/env var. The code is small; this ~15-minute setup is the gate before anything syncs.

## C5. Edge behaviors to expect (and to tell the owner up front)
- **Owner merges two contacts inside Google:** harmless under one-way. DashNex never reads it; future pushes update the surviving Google record by email.
- **Owner adds someone to Google first; they check in a week later:** if the Google entry's email matches the check-in email, the push UPDATES it (no dup). If the Google entry had a different email or none, you get ONE manual duplicate to merge. **Email consistency is what makes the whole thing self-dedup.**
- **A "returning / repeat customer" check-in** still syncs as an UPDATE (email upsert), never a new record. The best use of such a flag is to be *gentle*: don't overwrite good existing data with blanks (pairs with the "blank-wipe" safety rule — a blank field on a return visit should KEEP the prior value, not erase it).

## C6. How custom form data lands in Google (NOT just notes)
Google's People API is richer than the Notes field, so map deliberately instead of dumping everything into a note blob:
- **Short structured values** (Computer Type, Referral source, lead/client Status) → Google **`userDefined` custom fields** (labeled key/value pairs; they show under the contact's "Custom fields" section and are searchable). Keeps them structured.
- **Long free text** (the issue / Details narrative) → Google **`biographies` (Notes)**, but **APPEND into a clearly delimited block** (e.g. a `--- TCS ---` section) so you never erase notes the owner typed by hand (gate codes, device serials). Notes is a single field; treat it append-only, never clobber.
- **Status label** → also a Google **contactGroup / label** ("Clients", "Leads") so contacts cluster in Gmail and on the phone. That is the grouping payoff, for free.

Net: custom fields ride along as real labeled Google custom fields, long prose goes to Notes additively, and status doubles as a Google label. Nothing has to be an undifferentiated note dump.

## C7. Google Cloud OAuth setup (one-time, the owner's ~15 minutes)
The sync code is a NO-OP until three secrets exist: `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`, `GOOGLE_REFRESH_TOKEN`. Here is the exact, reproducible way to produce them. Do it once.

**Part 1 — Project + API**
1. [console.cloud.google.com](https://console.cloud.google.com) -> top project dropdown -> **New Project** (e.g. "TCS Contacts Sync") -> Create -> select it.
2. **APIs & Services -> Library** -> search **People API** -> **Enable**.

**Part 2 — Configure OAuth consent (now called "Google Auth Platform")**
> ⚠️ **Google redesigned this in 2025.** The old "OAuth consent screen" wizard is gone; it is now **Google Auth Platform** with a left nav: Overview, Branding, Audience, Clients, Data Access, Verification Center, Settings. The steps below match the NEW UI (verified live 2026-06-28). After enabling the People API, reach it via **APIs & Services -> OAuth consent screen** (redirects to Google Auth Platform) or the left-nav **OAuth consent screen** link on the People API page.
3. On "Google Auth Platform not configured yet," click **Get Started**.
4. Wizard **App Information**: App name (e.g. "TCS Contacts Sync") + **User support email** (pick from dropdown) -> **Next**.
5. Wizard **Audience**: choose **External** -> **Next**.
6. Wizard **Contact Information**: your email -> **Next**. Then **Finish**: check the User Data Policy agreement -> **Create**.
7. **PUBLISH THE APP. Critical, and it MOVED.** Left nav -> **Audience** -> **Publishing status** (shows "Testing") -> **Publish app** -> Confirm. *Why:* while "Testing," Google **expires the refresh token after 7 days** and the sync silently dies weekly. Published (even unverified) = long-lived token. Single-owner internal tool: unverified production is fine; you click through a one-time "Google hasn't verified this app" warning.
8. *(Usually not needed.)* Scopes live under left nav -> **Data Access**. We request `auth/contacts` dynamically in the Playground, so you can skip this. Only add the scope here if the Playground "Authorize APIs" step later refuses.

**Part 3 — OAuth client credentials (now under "Clients")**
9. Left nav -> **Clients** -> **Create client** (the old path was APIs & Services -> Credentials -> Create Credentials -> OAuth client ID; both reach the same place).
10. Application type: **Web application** (NOT Desktop — the Playground redirect flow needs a web client). Name it (e.g. "TCS Sync Web").
11. **Authorized redirect URIs** -> **+ Add URI** -> add exactly: `https://developers.google.com/oauthplayground` -> **Create**.
12. Copy the **Client ID** and **Client Secret**. (These are `GOOGLE_CLIENT_ID` / `GOOGLE_CLIENT_SECRET`.)

**Part 4 — Refresh token (one-time, via OAuth Playground)**
13. Open [developers.google.com/oauthplayground](https://developers.google.com/oauthplayground).
14. **Gear icon (top-right) -> check "Use your own OAuth credentials"** -> paste the Client ID + Client Secret. Confirm "Access type: Offline" and "Force prompt: consent screen" are on (guarantees a refresh token).
15. Left panel, bottom **"Input your own scopes"** box: enter `https://www.googleapis.com/auth/contacts` -> **Authorize APIs**.
16. Sign in with the owner's Google account -> click through "Google hasn't verified this app" (Advanced -> Go to ...) -> **Allow**.
17. Step 2 -> **"Exchange authorization code for tokens"** -> copy the **Refresh token**. (This is `GOOGLE_REFRESH_TOKEN`.)

**Part 5 — Store the secrets + go live**
18. `dashnex secrets set GOOGLE_CLIENT_ID=... GOOGLE_CLIENT_SECRET=... GOOGLE_REFRESH_TOKEN=...` (one per the CLI's syntax). `.env` is auto-generated; do NOT hand-edit it. Redeploy if needed.
19. Live-test with ONE contact change (e.g. re-add a placeholder contact) and confirm it appears in Google Contacts within seconds, with labels + custom fields. Expect possible first-run tweaks to **label (contactGroups) mapping** and **searchContacts eventual-consistency** (Google's contact search can lag a few seconds right after a write).

**Gotchas for the Errors & Gotchas log:**
- **WRONG-PROJECT trap (verified live, cost real time).** Google Cloud scopes everything to the *selected project*, shown in the **pill at top-left, right of the "Google Cloud" wordmark.** It is easy to enable the People API on project A, then land on the OAuth Platform page while project B is selected, and nothing lines up ("not configured," can't find your work). FIX: click the project pill -> "Select a project" -> pick the SAME project the People API was enabled on. TEACHING RULE: before every step, glance at the project pill and confirm it is the right project. Treat that pill as the anchor-of-anchors. (Also a clean example of anchor-then-preposition instruction: "top-left, right of the Google Cloud wordmark, is a pill showing the active project.")
- **"Testing" mode = 7-day refresh-token death.** Publish to production or the sync breaks weekly. (Biggest trap.)
- The `auth/contacts` scope is **sensitive**; unverified apps are capped (~100 users) but a single owner is fine. Full verification only matters if you ever distribute the app.
- Use a **Web application** client (not Desktop) so the Playground redirect URI flow works without running local code.
- `prompt=consent` + `access_type=offline` are required to receive a refresh token at all; if you only get an access token, you skipped these.

---
*Reusable rules of thumb. **Forms-in:** in a coded DashNex app, write to Contacts directly via the contacts service + the exported field/tag models (opt-in forms are only the bridge when you have NO server code). Normalize phone, pre-create fields in the dashboard, match by label, keep writes best-effort. **Data model:** one email (unique, required) / one phone / one address per contact; email is identity; tags + custom fields are the only "many." **Sync-out:** DashNex → Google only, real-time via contact events, strict email match, additive writes that never flatten Google's richer records; short data → Google custom fields, long data → appended Notes, status → a Google label.*
