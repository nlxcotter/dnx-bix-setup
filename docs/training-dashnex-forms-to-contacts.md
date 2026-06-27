# Training Module: Wiring Custom Forms into DashNex Contacts (leads, clients, custom fields)

> **Author:** TCS PM session. **Status:** living. **Audience:** anyone building a custom-coded DashNex Business app who wants their own forms to feed the built-in CRM (Contacts) with custom fields and lead/client labeling.
>
> **The one-line lesson:** On a *no-code* DashNex builder (PowerTech Site Builder), an Easy Opt-In form is the *only* bridge from a form into Contacts. On a *coded* DashNex app, you are not bound by that. You can call the contacts service directly, which keeps your own forms, validation, and notification emails intact. Use the built-in pieces (custom fields, tags, contact type) from the cleaner side: server code.

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
*Reusable rule of thumb: in a coded DashNex app, write to Contacts directly via the contacts service + the exported field/tag models. Opt-in forms are only the bridge when you have NO server code (the no-code builder). Normalize phone, pre-create fields in the dashboard, match by label, keep writes best-effort.*
