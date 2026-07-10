# KIN — Decision Log

A running record of product, design, and technical decisions. Newest first.

## 2026‑07‑10

- **UI direction:** Selected **Direction B — "Signal"** (dark, camera‑first,
  technical; mint `#37E1B4` accent, monospace labels). Tokens locked in
  [`design/design-tokens.md`](./design/design-tokens.md); applies to app + CRM.
  Directions A ("Ledger") and C ("Field") retained for record only.
- **Data export:** Added requirements FR‑10.1–FR‑10.11 and NFR‑9.5/9.6. Scanned
  data is exportable in **CSV** and **JSON/NDJSON** (analytics‑ready:
  Excel/Sheets/pandas/Power BI/Tableau/Looker), **XLSX** and **PDF** (viewing),
  and **vCard** (address‑book interop). All open, non‑proprietary formats with a
  published field dictionary; filtered‑view export supported.
- **OCR engine:** On‑device **Google ML Kit Text Recognition v2** (Apple Vision
  as iOS option) — free, offline, no API key. No cloud OCR.
- **Scanner UX:** Do **not** build scanning from scratch; wrap
  `react-native-vision-camera` (or Flutter `google_mlkit_text_recognition`).
  Own the field parser + review UX.
- **CRM:** **Next.js** (App Router) + Auth.js (**Google + Apple SSO**) +
  Prisma/Postgres. App shares the same Google/Apple identity via OIDC + PKCE →
  one account across app and web.
- **Offline‑first:** Local durable store + idempotent, retry‑with‑backoff sync
  queue. Scanning path makes zero network calls.
- **WhatsApp sharing:** Consumer `wa.me` / `whatsapp://` deep links only — no
  Business API, no key, no cost.
- **Language:** English ships on an i18n‑ready foundation (all strings
  externalized).
- **Scope:** v1 is single‑user; teams/shared workspaces are backlog.

## Open questions

- Single‑user v1 vs. teams from day one? (Currently: single‑user.)
- Repo/monorepo split for `apps/mobile` + `apps/crm` + `packages/shared`?
- Read‑only export API (FR‑10.10) in v1 or fast‑follow?
