# KIN — Requirements & Design Documentation

> **KIN** is a mobile app for digital cards. It scans other people's business
> cards, detects the data on the card **on-device**, and creates a contact in
> the **CRM** — a Next.js web application with its own backend and SSO
> (Google / Apple) login. The mobile app authenticates with the same Google /
> Apple identity so it talks to the same CRM account.

## Product in one paragraph

The phone is a **scanner + offline capture queue**. It never talks to a
third‑party OCR service, never needs an API key, and works with no
connectivity. Recognition happens locally using a free, open‑source, on‑device
engine (Google ML Kit on Android, Apple Vision on iOS). Captured cards are
stored locally and **queued**; when the device is online and authenticated they
sync to the CRM. In the CRM the user manages contacts, **filters and groups them
by tags**, sets **follow‑up reminders** for selected contacts, records
**relationship / person notes** about who the contact is and how they met, and
**shares a scanned card to WhatsApp**.

## Document map

| Document | Purpose |
|---|---|
| [`functional-requirements.md`](./functional-requirements.md) | What the system must **do** — features, flows, rules, acceptance criteria. |
| [`non-functional-requirements.md`](./non-functional-requirements.md) | How well it must do it — performance, security, privacy, offline, i18n, etc. |
| [`../research/existing-solutions.md`](../research/existing-solutions.md) | "Has this been done before?" — prior art, reusable open‑source OCR, and gap analysis. |
| [`../design/tech-architecture.md`](../design/tech-architecture.md) | Recommended stack, offline‑sync design, WhatsApp linking, auth. |
| [`../design/ui-mockups.md`](../design/ui-mockups.md) | Mobile UI mockup directions — **Direction B "Signal" selected** (interactive preview linked). |
| [`../design/design-tokens.md`](../design/design-tokens.md) | Locked color / type / spacing tokens for the chosen "Signal" identity (app + CRM). |
| [`../DECISIONS.md`](../DECISIONS.md) | Running log of product/design/tech decisions. |

## Scope of this deliverable

- ✅ Functional requirements document
- ✅ Non‑functional requirements document
- ✅ Prior‑art / existing‑solution research + gap analysis
- ✅ Recommended offline, no‑API‑key, open‑source scanning approach
- ✅ Mobile UI mockup **options** → **Direction B "Signal" selected** (tokens locked)
- ✅ English (i18n‑ready foundation)
- ✅ WhatsApp share of scanned‑card findings
- ✅ Data **export** for analytics / viewing (CSV, JSON/NDJSON, XLSX, PDF, vCard)

## Glossary

| Term | Meaning |
|---|---|
| **KIN app** | The mobile (iOS + Android) scanner/capture client. |
| **CRM** | The Next.js web app + backend + database where contacts live. |
| **Card** | A physical business card that is scanned. |
| **Contact** | The structured record created from a scanned card. |
| **OCR** | Optical Character Recognition — turning the card image into text. |
| **Capture queue** | Local, offline store of scans waiting to sync to the CRM. |
| **Tag** | A user‑defined label used to filter and group contacts. |
| **Follow‑up** | A reminder to contact/act on a contact at a future time. |
| **Person notes** | Free‑form + structured info about the individual (context, how you met, etc.). |
