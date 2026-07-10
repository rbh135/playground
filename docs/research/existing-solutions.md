# KIN — Prior Art & Existing‑Solution Research

**Question asked:** *"Has this been done before, so requirements can be reused,
or are there gaps to fill?"*

**Short answer:** Yes — business‑card scanning into a CRM is a **mature,
well‑solved space** both commercially and in open source. KIN does **not** need
to build OCR from scratch. The differentiators worth building are the
**offline‑first capture queue**, the **no‑API‑key / on‑device** constraint, and
the **relationship‑notes + WhatsApp** workflow. This doc surveys prior art,
recommends reusable open‑source components, and lists the gaps KIN must fill
itself.

---

## 1. Commercial prior art (for feature reuse, not integration)

| Product | Relevant, proven pattern to borrow |
|---|---|
| **HubSpot / Zoho / Sansan / CamCard / ABBYY Business Card Reader** | Scan → parse → editable review → CRM contact. Confirms the FR‑2.x flow. |
| **HiHello / Blinq / Popl / KADO** | Digital‑card + relationship notes ("where we met", tags, follow‑ups). Confirms FR‑3.x and FR‑5.x. |
| **CamCard / Sansan** | Tagging, grouping, and follow‑up reminders on scanned contacts. Confirms FR‑4.x, FR‑5.x. |

**Takeaway:** the feature set is validated. KIN's requirements align with
established products, so the FRD can lean on these as reference implementations.
KIN's edge is the **privacy/offline/no‑cost** posture and WhatsApp‑native
sharing.

---

## 2. Open‑source / free OCR that meets the constraints

Constraints: **on‑device, offline, no network call at scan time, no API key,
free.** These rule out cloud OCR (Google Cloud Vision, AWS Textract, Azure —
all need keys and network). The viable engines:

| Engine | Platform | Offline | API key | License / cost | Notes |
|---|---|---|---|---|---|
| **Google ML Kit — Text Recognition v2** | Android + iOS | ✅ on‑device | ❌ none | Free (bundled model) | **Recommended primary.** Fast, robust, no cloud, no key. Latin + more scripts. |
| **Apple Vision (`VNRecognizeTextRequest`)** | iOS only | ✅ on‑device | ❌ none | Free (OS framework) | Excellent iOS accuracy; use as the iOS engine or fallback. |
| **Tesseract** (`tesseract.js`, native bindings) | All | ✅ | ❌ none | Apache‑2.0 | Fully open source but slower/less accurate on cards; good offline fallback. |
| **PaddleOCR** | All (heavier) | ✅ | ❌ none | Apache‑2.0 | Strong accuracy, larger model footprint; multilingual. |

### Ready‑made wrapper libraries (don't reinvent the capture UI)

| Library | Stack | What it gives you |
|---|---|---|
| **react-native-vision-camera** + **vision-camera OCR plugin** (e.g. `@bear-block/vision-camera-ocr`) | React Native | Real‑time on‑device OCR via ML Kit; frame processing. |
| **`business_card_scanner`** (pub.dev) | Flutter | Card scan + smart parsing (email/phone/URL) via ML Kit, offline. |
| **`google_mlkit_text_recognition`** | Flutter | Raw ML Kit text recognition to build a custom parser on. |
| **CardScan** (danauclair) | iOS/Tesseract | Reference: OCR a card → create a contact. Dated but illustrative. |

**Recommendation:** use **ML Kit Text Recognition v2** as the cross‑platform
engine (with **Apple Vision** as the iOS‑native option), wired through
**react-native-vision-camera** *or* Flutter's `google_mlkit_text_recognition`.
The **raw text → structured fields** parser (name/title/company/phone/email/
URL/address) is thin, high‑value, and worth owning in‑house so KIN controls
accuracy and the "unassigned lines" UX (FR‑2.4).

---

## 3. Reusable building blocks for the rest of the stack

| Concern | Reusable, free/open option |
|---|---|
| **CRM framework** | Next.js (App Router) + route handlers — per the brief. |
| **Auth (Google + Apple SSO)** | **Auth.js / NextAuth** with Google + Apple providers; OIDC + PKCE for mobile. |
| **DB / ORM** | Postgres + **Prisma**. |
| **Offline mobile store + sync** | **WatermelonDB** (built for offline sync) or SQLite + a custom queue; **MMKV** for light state. |
| **Reminders** | OS local notifications (Notifee / expo-notifications) + server‑side schedule for web. |
| **WhatsApp share** | `wa.me` / `whatsapp://` deep links + OS share sheet — **no** Business API. |
| **vCard export** | Any MIT vCard builder, or hand‑rolled (format is trivial). |
| **i18n** | `i18next` / `react-i18next` (app) and `next-intl` (web). |

---

## 4. Gap analysis — what KIN must build itself

The market solves scanning; it does **not** hand you KIN's exact combination.
Gaps to fill:

1. **Offline‑first capture queue with idempotent sync** to a *self‑owned* CRM.
   Most scanners are cloud‑first; KIN's "scan works fully offline, sync later,
   no scan‑time network, no keys" is the core engineering gap. *(FR‑6.x, NFR‑6.x)*
2. **Card‑text → structured‑fields parser** tuned for KIN's review UX with an
   "unassigned lines" mapping step. *(FR‑2.3, FR‑2.4)*
3. **Unified identity across app + web** (same Google/Apple account → same CRM
   data). *(FR‑1.3)*
4. **Relationship / person‑notes model** captured at scan time (how/where met,
   custom fields, voice notes). Off‑the‑shelf scanners store card fields, not
   rich meeting context. *(FR‑3.x)*
5. **WhatsApp‑native sharing** of findings via consumer deep links (not the paid
   Business API). *(FR‑8.x)*
6. **Tag‑based filtering + grouping and a follow‑up dashboard** wired to both
   app and web through sync. *(FR‑4.x, FR‑5.x)*

---

## 5. Build‑vs‑reuse decision

| Component | Decision |
|---|---|
| OCR engine | **Reuse** — ML Kit / Apple Vision (free, offline, no key). |
| Camera + capture UI | **Reuse** wrapper (vision-camera / Flutter scanner), lightly themed. |
| Field parser + review UX | **Build** (thin, differentiating). |
| Offline store + sync queue | **Build on** WatermelonDB/SQLite (reuse engine, own the sync contract). |
| CRM (Next.js) + auth + DB | **Reuse** frameworks (Next.js, Auth.js, Prisma/Postgres), build the domain. |
| WhatsApp share / vCard | **Reuse** deep links + trivial builders. |
| i18n | **Reuse** i18next / next-intl. |

**Conclusion:** ~70% of KIN is assembled from proven free/open components; the
~30% that is bespoke (offline sync contract, parser/review UX, relationship
model, WhatsApp flow) is exactly where KIN's value lives.

---

## Sources

- [business_card_scanner — Flutter package (pub.dev)](https://pub.dev/packages/business_card_scanner)
- [ML Kit vs. OpenCV — document scanning (Scanbot)](https://scanbot.io/blog/ml-kit-vs-opencv-document-scanning-software/)
- [CardScan — Tesseract OCR business cards → iPhone contacts (GitHub)](https://github.com/danauclair/CardScan)
- [BusinessCardScanner (GitHub, philipmorrisintl)](https://github.com/philipmorrisintl/BusinessCardScanner)
- [Business-Card-Reader-App (GitHub, ierolsen)](https://github.com/ierolsen/Business-Card-Reader-App)
- [@bear-block/vision-camera-ocr (npm)](https://www.npmjs.com/package/@bear-block/vision-camera-ocr)
- [React Native OCR Scanner "Card2Contact" (AlamedaDev, Medium)](https://medium.com/alameda-dev/react-native-ocr-scanner-card2contact-6ed7b1e5f7c7)
- [React Native Business Card Scanner (Shivlab)](https://shivlab.com/blog/react-native-business-card-scanner/)
- [expo-ocr (GitHub, barthap)](https://github.com/barthap/expo-ocr)
