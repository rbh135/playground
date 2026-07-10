# KIN — Non‑Functional Requirements (NFRD)

**Product:** KIN — digital business‑card scanner + CRM
**Version:** 1.0 (draft)
**Date:** 2026‑07‑10
**Status:** For review

Non‑functional requirements describe *how well* the system behaves. IDs are
`NFR-x.y`. Each has a **target** that is testable/measurable where possible.

---

## 1. Performance & responsiveness

| ID | Requirement | Target |
|---|---|---|
| NFR‑1.1 | On‑device OCR latency for a single card. | ≤ **1.5 s** median, ≤ 3 s p95 on a mid‑range 2022+ phone. |
| NFR‑1.2 | Time from tapping capture to editable review screen. | ≤ **2.5 s** median. |
| NFR‑1.3 | App cold start to camera‑ready. | ≤ **2 s** on a mid‑range device. |
| NFR‑1.4 | CRM contact‑list initial render (100 contacts). | ≤ **1 s** on a broadband connection (TTI). |
| NFR‑1.5 | Sync flush of a single queued contact (text only). | ≤ **1 s** server round‑trip on a normal 4G link. |
| NFR‑1.6 | The app remains usable (scan + review) with **0** connectivity. | 100% of capture flow works offline. |

## 2. Scalability & capacity

| ID | Requirement | Target |
|---|---|---|
| NFR‑2.1 | Contacts per account handled without UI degradation. | ≥ **10,000** contacts with virtualized lists. |
| NFR‑2.2 | Local offline queue depth. | ≥ **500** pending captures stored without loss. |
| NFR‑2.3 | CRM backend horizontal scalability. | Stateless API (Next.js route handlers / serverless) scales with load; no sticky sessions. |
| NFR‑2.4 | Image storage. | Object storage (e.g. S3‑compatible) with lifecycle rules; DB stores references, not blobs. |

## 3. Reliability & data integrity

| ID | Requirement | Target |
|---|---|---|
| NFR‑3.1 | No captured card is ever lost due to crash, kill, or connectivity loss. | Durable local write **before** UI confirms save. |
| NFR‑3.2 | Sync is **idempotent** — retries never create duplicates. | Client‑generated stable IDs / dedupe keys enforced server‑side. |
| NFR‑3.3 | Sync recovers automatically from transient failures. | Exponential backoff with jitter; resumes on connectivity regained. |
| NFR‑3.4 | CRM data durability. | Managed Postgres with automated backups + point‑in‑time recovery. |
| NFR‑3.5 | Target service availability (CRM). | **99.5%** monthly for v1. |

## 4. Security

| ID | Requirement | Target |
|---|---|---|
| NFR‑4.1 | All client↔server traffic encrypted in transit. | **TLS 1.2+** enforced; HSTS on the web CRM. |
| NFR‑4.2 | Data encrypted at rest. | DB + object storage encryption at rest; tokens in **Keychain (iOS) / Keystore (Android)**. |
| NFR‑4.3 | Auth uses **OAuth 2.0 / OIDC** via Google & Apple; no passwords stored by KIN. | Standard PKCE flow for the mobile app; short‑lived access + rotating refresh tokens. |
| NFR‑4.4 | Users can only access **their own** data. | Every CRM API enforces per‑account authorization (row‑level ownership checks). |
| NFR‑4.5 | The OCR pipeline sends **no data off device** and needs **no API key**. | Verified by network capture in airplane mode + static build check (no OCR keys/endpoints). |
| NFR‑4.6 | Secrets (OAuth client secrets, DB creds) never ship in the mobile binary. | Secrets live server‑side only; mobile uses public OAuth client + PKCE. |
| NFR‑4.7 | Standard web hardening on the CRM. | CSRF protection, secure/HttpOnly/SameSite cookies, security headers, input validation. |
| NFR‑4.8 | Dependency & supply‑chain hygiene. | Automated dependency scanning; pinned lockfiles. |

## 5. Privacy & compliance

| ID | Requirement | Target |
|---|---|---|
| NFR‑5.1 | On‑device processing by default; card data leaves the device only to reach the user's own CRM. | No third‑party analytics receive card contents. |
| NFR‑5.2 | GDPR/CCPA support: access, export, and erasure of personal data. | Self‑service export + delete (FR‑1.7, FR‑7.3, FR‑7.6). |
| NFR‑5.3 | Clear consent + rationale for camera, notifications, location. | Contextual permission prompts (FR‑9.2); location is optional. |
| NFR‑5.4 | Data minimization. | Only fields needed for a contact are stored; raw images deletable per contact. |
| NFR‑5.5 | Lawful handling of **third‑party** personal data (the scanned person). | Privacy policy states purpose (personal CRM); retention & deletion honored. |
| NFR‑5.6 | App‑store privacy disclosures accurate. | iOS Privacy Nutrition Label / Android Data Safety reflect actual data flows. |

## 6. Offline‑first & sync quality

| ID | Requirement | Target |
|---|---|---|
| NFR‑6.1 | Full capture, edit, tag, note and reminder creation available offline. | 100% of these flows function with no network. |
| NFR‑6.2 | Local store is the source of truth on device until synced. | Reads served locally; UI never blocks on the network. |
| NFR‑6.3 | Queue survives app updates and reboots. | Persisted in durable local DB (SQLite/WatermelonDB/MMKV), migration‑safe. |
| NFR‑6.4 | User can see and control sync (retry, sync‑on‑Wi‑Fi‑only). | Sync status UI + cellular toggle (FR‑6.5, FR‑9.3). |

## 7. Usability & accessibility

| ID | Requirement | Target |
|---|---|---|
| NFR‑7.1 | A first‑time user can scan and save a contact without instructions. | ≥ 90% task success in usability testing. |
| NFR‑7.2 | Capture is one‑handed and fast in the field. | Core scan action reachable within thumb zone; ≤ 3 taps to saved contact. |
| NFR‑7.3 | Accessibility. | WCAG 2.1 AA for the web CRM; dynamic type, VoiceOver/TalkBack labels, ≥ 4.5:1 contrast in the app. |
| NFR‑7.4 | Error states are actionable and human. | Failures explain cause + next step (e.g. "couldn't read card — retake or edit manually"). |
| NFR‑7.5 | Consistent design system across app + web. | Shared tokens (color, spacing, type) documented in the design doc. |

## 8. Internationalization

| ID | Requirement | Target |
|---|---|---|
| NFR‑8.1 | English ships; the codebase is i18n‑ready. | All UI text externalized to locale catalogs; no hardcoded strings. |
| NFR‑8.2 | Locale‑aware formatting. | Dates, times, phone numbers formatted per locale; E.164 phone normalization. |
| NFR‑8.3 | Layout tolerant of text expansion + RTL‑ready. | No truncation at +30% string length; layout mirror‑safe for future RTL. |

## 9. Portability & compatibility

| ID | Requirement | Target |
|---|---|---|
| NFR‑9.1 | Mobile OS support. | iOS 15+ and Android 9 (API 28)+. |
| NFR‑9.2 | Single cross‑platform mobile codebase preferred. | React Native or Flutter (see architecture doc) to share app logic. |
| NFR‑9.3 | CRM browser support. | Latest 2 versions of Chrome, Safari, Edge, Firefox; responsive/mobile‑web friendly. |
| NFR‑9.4 | OCR engine is bundled and version‑pinned. | On‑device model shipped with the app; deterministic across the supported OS range. |

## 10. Maintainability & observability

| ID | Requirement | Target |
|---|---|---|
| NFR‑10.1 | Automated tests. | Unit tests for parsing/sync/queue logic; e2e for the scan→CRM happy path. |
| NFR‑10.2 | CI/CD. | Lint + typecheck + test gate on every PR; reproducible builds. |
| NFR‑10.3 | Observability (server). | Structured logs, error tracking, and sync‑failure metrics — **no card content** in logs/telemetry. |
| NFR‑10.4 | Privacy‑respecting product analytics. | Only aggregate/opt‑in events; never PII or card contents. |
| NFR‑10.5 | Documentation. | Requirements, architecture, and API contract kept in‑repo and current. |

## 11. Cost constraints

| ID | Requirement | Target |
|---|---|---|
| NFR‑11.1 | No paid OCR/scanning SDKs or per‑scan fees. | OCR engine is free & open‑source / OS‑provided (ML Kit / Apple Vision). |
| NFR‑11.2 | No paid WhatsApp Business API for sharing. | Consumer WhatsApp deep links only (FR‑8.2). |
| NFR‑11.3 | Infrastructure runs on cost‑efficient managed/serverless tiers. | Next.js hosting + managed Postgres + object storage within a modest budget. |

---

## 12. Assumptions & dependencies

- Users have a Google or Apple account (required for SSO).
- WhatsApp sharing assumes the consumer WhatsApp app is installed on the device.
- On‑device OCR accuracy depends on card legibility and lighting; the editable
  review screen (FR‑2.4) is the safety net for imperfect recognition.
- "No network calls" applies to the **scanning/OCR** step; **sync to the CRM**
  is intentionally a network operation (queued and deferred).
- Location capture is optional and off unless the user grants permission.
