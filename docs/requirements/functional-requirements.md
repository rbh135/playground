# KIN — Functional Requirements (FRD)

**Product:** KIN — digital business‑card scanner + CRM
**Version:** 1.0 (draft)
**Date:** 2026‑07‑10
**Status:** For review

Each requirement has a stable ID (`FR-x.y`), a priority
(**M**=Must / **S**=Should / **C**=Could, MoSCoW), and acceptance criteria.
Requirements are grouped by subsystem: **App (mobile scanner)**,
**CRM (Next.js web + backend)**, and **shared** capabilities.

---

## 1. Actors & roles

| Actor | Description |
|---|---|
| **User** | A person who scans cards and manages their own contacts. Single primary role at launch. |
| **CRM account** | The identity (Google or Apple SSO) that ties the app and the CRM together. |
| **System** | Background sync, OCR engine, reminder scheduler, notification service. |
| *(future)* **Team / Org admin** | Manages shared contacts and members — **out of scope for v1**, noted for design headroom. |

---

## 2. Authentication & account (shared)

| ID | Priority | Requirement | Acceptance criteria |
|---|---|---|---|
| FR‑1.1 | M | The CRM shall allow sign‑in via **Google SSO**. | User can complete Google OAuth and land in the CRM authenticated. |
| FR‑1.2 | M | The CRM shall allow sign‑in via **Apple SSO** (Sign in with Apple). | User can complete Apple OAuth and land authenticated. |
| FR‑1.3 | M | The mobile app shall log in with **Google or Apple**, producing the **same CRM identity** as the web. | Signing into the app with the same provider account shows the same contacts as the web CRM. |
| FR‑1.4 | M | The app shall obtain and store a **session/refresh token** so the user is not forced to re‑auth on every launch. | After first login, relaunching the app within the token validity keeps the user signed in. |
| FR‑1.5 | M | The app shall let the user **capture cards while signed out / offline**; captures are held locally and attributed to the account once authenticated. | With no session, a scan is saved locally; after sign‑in it syncs under that account. |
| FR‑1.6 | S | The user shall be able to **sign out**, which clears tokens but prompts before discarding any **unsynced** captures. | Sign‑out with pending queue shows a warning listing unsynced count. |
| FR‑1.7 | C | The user shall be able to **delete their account and data** (GDPR right to erasure) from the CRM. | Account deletion removes contacts, images and reminders after confirmation. |

---

## 3. Card scanning & capture (App)

| ID | Priority | Requirement | Acceptance criteria |
|---|---|---|---|
| FR‑2.1 | M | The app shall capture a business card via the **device camera** with edge/framing guidance. | Camera screen shows a card frame guide and a capture button. |
| FR‑2.2 | M | The app shall perform **on‑device OCR** on the captured image with **no network call and no API key**. | With the device in airplane mode, a scan still extracts text. |
| FR‑2.3 | M | The app shall **auto‑detect and structure** fields from the OCR text: **name, job title, company, phone(s), email(s), website, address, other**. | For a standard card, at least name, one phone and one email are auto‑populated. |
| FR‑2.4 | M | The app shall present an **editable review screen** so the user can correct/confirm parsed fields before saving. | Every detected field is editable; unparsed lines are shown as "unassigned" text the user can map. |
| FR‑2.5 | M | The app shall **retain the original card image** (front, and optionally back) attached to the contact. | The saved contact shows the source image; back image can be added. |
| FR‑2.6 | S | The app shall support **importing an existing photo** of a card from the gallery (same OCR pipeline). | Selecting a gallery image runs OCR and reaches the review screen. |
| FR‑2.7 | S | The app shall support **multi‑capture / batch mode** (scan several cards in a row, review later). | User can take N scans; each appears as a pending item to review. |
| FR‑2.8 | S | The app shall detect **duplicate contacts** (same email/phone/name) and offer merge instead of creating a new record. | Scanning a card whose email already exists prompts "merge or create new". |
| FR‑2.9 | C | The app shall detect the **card's language/script** and still extract Latin‑script fields; non‑Latin text is captured as raw notes. | A card with mixed scripts still extracts Latin email/phone. |
| FR‑2.10 | C | The app shall let the user **scan a QR / vCard code** on the card as an alternative high‑accuracy path. | A card with a vCard QR fills fields from the QR payload. |

---

## 4. Person / relationship info (the "who is this person" section)

| ID | Priority | Requirement | Acceptance criteria |
|---|---|---|---|
| FR‑3.1 | M | Each contact shall have a dedicated **Person / Relationship section** distinct from the card‑derived fields. | Contact detail shows a separate "About this person" area. |
| FR‑3.2 | M | The person section shall include: **where/how met, event/context, date met, free‑form notes**. | These fields are savable and shown on the contact. |
| FR‑3.3 | M | The person section shall support **custom key/value fields** the user defines (e.g. "referred by", "interests"). | User can add a labeled custom field and value. |
| FR‑3.4 | S | Person notes shall be **capturable at scan time** (a quick "add context" step right after scanning). | Post‑scan flow offers an optional "where did you meet?" prompt. |
| FR‑3.5 | S | The person section shall support **voice‑to‑text** note entry for quick capture in the field. | Tapping mic dictates into the notes field (uses OS dictation). |
| FR‑3.6 | C | The app shall auto‑stamp **capture location** (with permission) to help remember where the card was collected. | If location permission granted, "met near {place}" is suggested. |

---

## 5. Tags, filtering & grouping (CRM + App)

| ID | Priority | Requirement | Acceptance criteria |
|---|---|---|---|
| FR‑4.1 | M | The user shall be able to **create, rename, color and delete tags**. | Tag management screen supports CRUD with a color per tag. |
| FR‑4.2 | M | The user shall be able to **assign multiple tags** to a contact (at scan time and later). | A contact can hold ≥1 tags; adding/removing persists. |
| FR‑4.3 | M | The CRM shall let the user **filter contacts by one or more tags** (AND/OR). | Selecting two tags with AND shows only contacts having both. |
| FR‑4.4 | M | The CRM shall let the user **group the contact list by tag** (grouped/section view). | A "group by tag" view lists contacts under tag headers. |
| FR‑4.5 | S | The CRM shall support **full‑text search** across name, company, email, phone and notes. | Typing a company name returns matching contacts. |
| FR‑4.6 | S | The CRM shall support **combined filters** (tag + reminder status + date met + text). | User can filter "tag=Investor AND has follow‑up AND met after June". |
| FR‑4.7 | S | Tags created in the app **sync** to the CRM and vice‑versa. | A tag added offline appears in the web CRM after sync. |
| FR‑4.8 | C | The system shall **suggest tags** based on card content (e.g. company domain, job title keywords). | Scanning a card from a known company suggests a matching tag. |

---

## 6. Follow‑up reminders (CRM + App)

| ID | Priority | Requirement | Acceptance criteria |
|---|---|---|---|
| FR‑5.1 | M | The user shall be able to **set a follow‑up reminder** on any contact with a **date/time**. | A contact can hold a due date; it appears in a reminders list. |
| FR‑5.2 | M | The system shall **notify** the user when a follow‑up is due (push / local notification on device; in‑app badge on web). | At due time the user receives a notification referencing the contact. |
| FR‑5.3 | M | The user shall be able to **mark a follow‑up done, snooze, or reschedule** it. | Each reminder offers done / snooze (e.g. +1d, +1w) / edit. |
| FR‑5.4 | S | The user shall be able to set **preset follow‑up windows** at scan time (e.g. "remind me in 3 days"). | Post‑scan flow offers quick reminder chips. |
| FR‑5.5 | S | The CRM shall provide a **"Follow‑ups" dashboard** (overdue / today / upcoming). | A dedicated view buckets reminders by due state. |
| FR‑5.6 | S | Reminders shall carry an optional **note / reason** ("send proposal"). | Reminder shows the note in the notification and list. |
| FR‑5.7 | C | The system shall support **recurring** follow‑ups (e.g. quarterly check‑in). | A recurring reminder regenerates after completion. |

---

## 7. Offline capture & sync (App ⇄ CRM)

| ID | Priority | Requirement | Acceptance criteria |
|---|---|---|---|
| FR‑6.1 | M | All scans, edits, tags, notes and reminders created in the app shall be **persisted locally first** (offline‑first). | Force‑quitting after an offline scan preserves the scan on relaunch. |
| FR‑6.2 | M | The app shall maintain an **upload/sync queue** that flushes to the CRM when connectivity + auth are available. | Going online after offline scans uploads them automatically. |
| FR‑6.3 | M | Sync shall be **resilient**: retry with backoff, resumable, and idempotent (no duplicate contacts on retry). | Killing the network mid‑sync and restoring it does not duplicate contacts. |
| FR‑6.4 | M | The scanning/OCR path shall make **zero network calls** and require **no API key**. | Network inspection during a scan shows no outbound OCR traffic. |
| FR‑6.5 | S | The app shall show **sync status per item** (queued / syncing / synced / failed) and an overall indicator. | Each pending card shows its state; failures are actionable (retry). |
| FR‑6.6 | S | Sync shall handle **conflict resolution** when the same contact is edited on web and app. | A conflicting edit surfaces a "keep mine / keep theirs / merge" choice or last‑write‑wins with history. |
| FR‑6.7 | S | Card images shall sync **separately/lazily** from text so contacts appear fast on slow links. | On a slow link, contact text appears before the image finishes uploading. |

---

## 8. Contact management (CRM)

| ID | Priority | Requirement | Acceptance criteria |
|---|---|---|---|
| FR‑7.1 | M | The CRM shall list all of the account's contacts with card‑derived and person fields. | Web list renders synced contacts with name/company/tags. |
| FR‑7.2 | M | The user shall be able to **view, edit and delete** a contact and its fields. | Edits on web persist and sync to the app. |
| FR‑7.3 | S | The user shall be able to **export** a contact as **vCard (.vcf)** and add to the device address book. | Export produces a valid vCard importable by iOS/Android/Google Contacts. |
| FR‑7.4 | S | The CRM shall support **bulk actions** (tag, delete, export, set follow‑up) on selected contacts. | Selecting N contacts and applying a tag updates all. |
| FR‑7.5 | S | The CRM shall show an **activity timeline** per contact (scanned, edited, followed‑up, shared). | Contact page lists dated events. |
| FR‑7.6 | C | The CRM shall support **CSV import/export** of contacts. | Uploading a CSV creates contacts; export downloads all. |

---

## 9. WhatsApp sharing (App + CRM)

| ID | Priority | Requirement | Acceptance criteria |
|---|---|---|---|
| FR‑8.1 | M | The user shall be able to **share a scanned card's findings to WhatsApp** — a formatted message with the extracted contact details. | "Share to WhatsApp" opens WhatsApp with a prefilled, readable summary. |
| FR‑8.2 | M | Sharing shall use **WhatsApp deep links** (`https://wa.me/?text=` / `whatsapp://send`) with no WhatsApp API key or Business API dependency. | Share works with only the consumer WhatsApp app installed. |
| FR‑8.3 | S | The user shall be able to **share the card image** and/or a **vCard** to WhatsApp via the OS share sheet. | Share sheet offers WhatsApp as a target for image + .vcf. |
| FR‑8.4 | S | The user shall be able to **message the contact directly on WhatsApp** using the card's phone number (`wa.me/<number>`). | If the card has a phone, "Message on WhatsApp" opens a chat to that number. |
| FR‑8.5 | C | The share message shall be **templated and localized** (user can edit the template). | User can adjust what fields the WhatsApp summary includes. |

---

## 10. Onboarding, settings & i18n

| ID | Priority | Requirement | Acceptance criteria |
|---|---|---|---|
| FR‑9.1 | M | The app UI shall be in **English** and built on an **i18n framework** so further languages can be added without code changes. | All strings resolve from a locale catalog; adding a locale file changes the UI language. |
| FR‑9.2 | M | First‑run onboarding shall explain **camera, notification and (optional) location** permissions and request them contextually. | Permissions are requested at point of use with rationale, not all upfront. |
| FR‑9.3 | S | Settings shall expose: default reminder window, WhatsApp share template, image quality, sync‑on‑cellular toggle, sign‑out. | Each setting persists and takes effect. |
| FR‑9.4 | S | The app shall provide an **empty‑state / first‑scan tutorial**. | A new user sees guidance to take their first scan. |

---

## 11. Data export & interoperability

The user must be able to get their scanned data **out** of KIN in formats that
are (a) usable by other analytics / BI / spreadsheet software and (b) fine for
plain viewing. Exports cover card fields **and** the KIN‑specific data (tags,
person notes, follow‑ups, dates, source).

| ID | Priority | Requirement | Acceptance criteria |
|---|---|---|---|
| FR‑10.1 | M | The CRM shall export contacts as **CSV** with a stable, documented column schema, UTF‑8 (BOM‑optional), **ISO‑8601 dates**, and **E.164 phone** normalization. | Exported CSV opens cleanly in Excel / Google Sheets / pandas with correct columns and no mangled encoding. |
| FR‑10.2 | M | The CSV/tabular export shall include **all analytically useful fields**: name, title, company, phones, emails, website, address, **tags** (delimited or one‑hot), **date met / where met**, **follow‑up due & status**, created/updated timestamps, and source (scan/manual). | Each field appears as its own column (or a documented multi‑value encoding). |
| FR‑10.3 | M | The CRM shall export contacts as **JSON** (structured) and **NDJSON / JSON‑Lines** (one record per line) for programmatic and analytics ingestion. | Exported JSON validates against the published schema; NDJSON streams row‑by‑row into analytics tools. |
| FR‑10.4 | S | The CRM shall export as **XLSX (Excel)** with typed columns and a header row, suitable for direct viewing/pivoting. | Opening the file in Excel shows typed cells (dates as dates, numbers as numbers). |
| FR‑10.5 | S | The user shall be able to export the **current filtered/grouped view** (by tag, follow‑up status, date, search), not only the full dataset. | Applying a tag filter then exporting yields only those contacts. |
| FR‑10.6 | S | The CRM shall export a **human‑readable PDF** of a contact or a filtered list (for viewing/printing/sharing), including the card image. | Generated PDF renders contact details + card image legibly. |
| FR‑10.7 | M | The app/CRM shall export **vCard 3.0/4.0 (.vcf)** for a contact or a batch, importable by iOS/Android/Google/Outlook contacts. | Exported vCard imports without errors into the OS address book. |
| FR‑10.8 | S | Export shall be available from **both the CRM (download)** and the **app (OS share sheet → WhatsApp / email / files)**. | User can share an exported file to WhatsApp or email from the app. |
| FR‑10.9 | S | The system shall publish an **export field dictionary / schema** (column names, types, meanings) so downstream tools can map fields reliably. | A schema doc/endpoint documents every exported field. |
| FR‑10.10 | C | The CRM shall support a **stable, read‑only export API endpoint** (authenticated) returning JSON/CSV for automated pulls into BI tools. | An authorized GET returns the account's contacts as JSON/CSV. |
| FR‑10.11 | C | Card **images** may be exported in bulk (zip) alongside a manifest linking image → contact. | A zip export contains images plus a manifest CSV/JSON. |

> **Design note:** CSV + NDJSON/JSON are the "adequate analytics formats" (they
> load directly into Excel, Google Sheets, pandas, Power BI, Tableau, Looker,
> etc.); XLSX and PDF are the "for viewing" formats; vCard is for address‑book
> interoperability. All are open, non‑proprietary, and require no paid tooling
> (NFR‑11).

---

## 12. Traceability summary

| Business ask | Requirements |
|---|---|
| Scan cards & detect data | FR‑2.1 – FR‑2.10 |
| Save information | FR‑2.4, FR‑2.5, FR‑6.1, FR‑7.1 |
| Filter & group by tags | FR‑4.1 – FR‑4.8 |
| Follow‑up reminders | FR‑5.1 – FR‑5.7 |
| Person info section | FR‑3.1 – FR‑3.6 |
| Create contact in CRM | FR‑2.4, FR‑6.2, FR‑7.1 |
| App = scanner, CRM = Next.js web+backend, same account | FR‑1.1 – FR‑1.5, FR‑6.2 |
| Offline, queued upload, no network calls / no API key | FR‑2.2, FR‑2.4, FR‑6.1 – FR‑6.4 |
| Open‑source / free scanner | FR‑2.2 (+ research doc) |
| English | FR‑9.1 |
| WhatsApp sharing | FR‑8.1 – FR‑8.5 |
| Export for analytics / viewing | FR‑10.1 – FR‑10.11 |

---

## 13. Out of scope for v1 (backlog)

- Team / multi‑user shared workspaces and role‑based permissions.
- Two‑way calendar/email integration (Gmail, Outlook) and automated sequences.
- AI enrichment from the web (company data lookup) — would violate the "no external calls" spirit unless explicitly opted in.
- Physical/NFC *own* digital card sharing (KIN is a scanner first; broadcasting your own card is a later phase).
- Languages beyond English (framework is prepared per FR‑9.1, but only English ships).
