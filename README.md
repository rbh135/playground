# KIN

**KIN** is a mobile app for digital cards. It scans other people's business
cards, detects the data **on‑device**, and creates a contact in the **CRM** — a
Next.js web app with its own backend and **Google / Apple SSO**. The mobile app
signs in with the same identity, so it talks to the same CRM account.

The phone is an **offline‑first scanner + capture queue**: recognition happens
locally (no network calls, no API key), scans are stored and **queued**, and
they sync to the CRM when online. In the CRM you manage contacts, **filter and
group by tags**, set **follow‑up reminders**, record **relationship / person
notes**, **export** for analytics or viewing, and **share to WhatsApp**.

> This repository currently contains the **requirements & design package**
> (the decisions below). Implementation follows the recommended architecture.

## What's decided

- **UI identity:** Direction **B — "Signal"** (dark, camera‑first, technical).
  → [`docs/design/design-tokens.md`](docs/design/design-tokens.md) ·
  [interactive mockups](https://claude.ai/code/artifact/7d56e76b-843a-40c2-93f8-f947b78f414a)
- **Scanning:** on‑device **ML Kit / Apple Vision** — free, offline, no API key.
- **CRM:** **Next.js** + Auth.js (Google + Apple SSO) + Prisma/Postgres.
- **Sync:** offline‑first local store + idempotent queued sync.
- **Sharing:** consumer **WhatsApp** deep links (no Business API).
- **Export:** CSV, JSON/NDJSON, XLSX, PDF, vCard — analytics‑ready + for viewing.
- **Language:** English on an i18n‑ready foundation.

## Documentation

| Doc | What's in it |
|---|---|
| [`docs/requirements/README.md`](docs/requirements/README.md) | Index + product overview + glossary. |
| [`docs/requirements/functional-requirements.md`](docs/requirements/functional-requirements.md) | Functional requirements (FRD) with acceptance criteria & traceability. |
| [`docs/requirements/non-functional-requirements.md`](docs/requirements/non-functional-requirements.md) | Non‑functional requirements (NFRD). |
| [`docs/research/existing-solutions.md`](docs/research/existing-solutions.md) | Prior art + build‑vs‑reuse + free OCR options. |
| [`docs/design/tech-architecture.md`](docs/design/tech-architecture.md) | Recommended stack & offline‑sync design. |
| [`docs/design/ui-mockups.md`](docs/design/ui-mockups.md) | UI directions (Direction B selected). |
| [`docs/design/design-tokens.md`](docs/design/design-tokens.md) | Locked "Signal" color / type / spacing tokens. |
| [`docs/DECISIONS.md`](docs/DECISIONS.md) | Decision log. |

## Planned structure (when build starts)

```
/apps
  /mobile   # React Native (or Flutter) offline scanner app
  /crm      # Next.js CRM (web + API route handlers)
/packages
  /shared   # shared types, parser, sync contract, i18n catalogs
/docs       # requirements & design (this package)
```
