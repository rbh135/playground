# KIN — Recommended Technical Architecture

**Purpose:** translate the requirements into a concrete, low‑cost, constraint‑
satisfying stack. This is a recommendation for review, not a locked decision.

---

## 1. High‑level shape

```
┌──────────────────────────────┐        ┌───────────────────────────────┐
│        KIN mobile app         │        │        KIN CRM (Next.js)      │
│  (iOS + Android, offline‑1st) │        │   web + backend + database    │
│                               │        │                               │
│  Camera ─► on‑device OCR ─────┤        │  Auth.js (Google + Apple SSO) │
│  (ML Kit / Apple Vision)      │        │  Route handlers / API         │
│        │  NO network, NO key  │        │  Prisma ─► Postgres           │
│        ▼                      │  sync  │  Object storage (images)      │
│  Field parser ─► Review UI    │◄──────►│  Reminders scheduler          │
│        │                      │  HTTPS │                               │
│  Local DB (WatermelonDB/      │  OIDC  │  Contacts · Tags · Follow‑ups │
│  SQLite) + Sync queue         │        │  Person notes · Activity      │
└──────────────────────────────┘        └───────────────────────────────┘
                 │                                       ▲
                 └── WhatsApp deep link (wa.me) ─────────┘ (share findings)
```

**Key principle:** the phone is **offline‑first**. OCR + capture never touch the
network. Sync is a **separate, deferred, idempotent** operation to the user's
own CRM.

---

## 2. Mobile app

- **Framework:** **React Native** (recommended) or Flutter. RN pairs cleanly with
  `react-native-vision-camera` + an ML Kit OCR frame processor and shares TS
  logic with the web team; Flutter's `google_mlkit_text_recognition` /
  `business_card_scanner` is an equally valid path.
- **OCR:** **Google ML Kit Text Recognition v2** (Android + iOS, on‑device, free,
  no key). Optionally **Apple Vision** as the iOS engine. Model is **bundled** —
  works in airplane mode.
- **Parser:** in‑house `rawText → { name, title, company, phones[], emails[],
  websites[], address, unassigned[] }`. Uses regex + heuristics (email/URL/phone
  patterns, line position, keyword cues) and hands anything ambiguous to the
  review screen as editable "unassigned" lines.
- **Local store:** **WatermelonDB** (or SQLite) as the durable source of truth;
  every scan is written locally **before** the UI confirms (NFR‑3.1).
- **Sync queue:** append‑only outbox with client‑generated stable IDs (dedupe
  key) → idempotent upserts server‑side. Exponential backoff + jitter; images
  uploaded lazily/separately (FR‑6.7). Respects Wi‑Fi‑only toggle.
- **Notifications:** `expo-notifications` / Notifee for local follow‑up reminders.

## 3. CRM (Next.js)

- **App Router** with **route handlers** as the backend API (stateless →
  scales horizontally, NFR‑2.3).
- **Auth:** **Auth.js (NextAuth)** with **Google** and **Apple** providers. The
  mobile app uses the **same providers via OIDC + PKCE**, exchanges the ID token
  for a KIN session/refresh token → **one identity, one dataset** across app and
  web (FR‑1.3).
- **Data:** **Postgres** via **Prisma**. Core entities:
  `User`, `Contact`, `CardImage`, `Tag`, `ContactTag`, `PersonNote`,
  `FollowUp`, `ActivityEvent`, `SyncCursor`.
- **Images:** S3‑compatible object storage; DB holds references (NFR‑2.4).
- **Authorization:** every query scoped by `userId` (row‑level ownership,
  NFR‑4.4).
- **Reminders:** due‑date scheduler (cron/queue) drives web notifications; the
  app schedules local notifications from the same data.

## 4. Sync contract (sketch)

- `POST /api/sync` — client sends the outbox batch (contacts, tags, notes,
  reminders) keyed by stable client IDs + `updatedAt`; server upserts
  idempotently and returns server IDs + a new cursor.
- `GET /api/sync?cursor=…` — pulls changes since the cursor for
  multi‑device / web‑edit propagation.
- **Conflict policy:** last‑write‑wins by `updatedAt` with an activity‑log audit;
  surface a merge choice for hard conflicts (FR‑6.6).

## 5. WhatsApp sharing

- **Text findings:** open `https://wa.me/?text=<url‑encoded summary>` (or
  `whatsapp://send?text=…`) with a localized, templated summary of the parsed
  card (FR‑8.1, FR‑8.5).
- **Message the contact:** `https://wa.me/<E.164 number>` from the card's phone
  (FR‑8.4).
- **Image / vCard:** OS share sheet → WhatsApp target (FR‑8.3).
- **No** WhatsApp Business API, **no** key, **no** cost (NFR‑11.2).

## 6. How the constraints are satisfied

| Constraint | How |
|---|---|
| Works offline | Local‑first store; OCR is on‑device. |
| Queue upload to CRM | Outbox + background sync with backoff. |
| No network calls (scan) | ML Kit / Apple Vision bundled models; network only at sync time. |
| No API key / no cost | On‑device OCR + consumer WhatsApp links + free frameworks. |
| Same account app↔web | Shared Google/Apple OIDC identity → same CRM user. |
| English + future locales | i18next (app) / next‑intl (web), all strings externalized. |

## 7. Suggested repo layout (when build starts)

```
/apps
  /mobile        # React Native (or Flutter) scanner app
  /crm           # Next.js CRM (web + API route handlers)
/packages
  /shared        # shared types, parser, sync contract, i18n catalogs
/docs            # these requirements & design docs
```
