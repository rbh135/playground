# KIN — Mobile UI Mockup Directions (choose one)

Three visual directions for the KIN mobile app, each shown across the three
screens that matter most: **Capture → Review → Contact**. All three cover every
required feature (offline capture, editable review, tags, follow-up reminders,
person notes, WhatsApp share) — the choice is about **tone**, and the winning
palette + type become the shared design tokens across the app **and** the
Next.js CRM.

> **Interactive preview (rendered phone screens):**
> https://claude.ai/code/artifact/7d56e76b-843a-40c2-93f8-f947b78f414a

---

## Direction A — "Ledger"  ·  professional / trustworthy

Calm warm-paper neutral, a confident blue, and a serif for names. Reads like a
serious relationship tool.

- **Palette:** `#F6F4EF` paper · `#1C2530` ink · `#2E5CB8` blue accent · `#E7EEF9` accent-soft · `#25D366` WhatsApp
- **Type:** serif for names/headings, system sans for body.
- **Best if** KIN targets sales, consulting, finance — users who want it to feel serious and boardroom-safe.

## Direction B — "Signal"  ·  fast / technical / camera-first

Dark viewfinder aesthetic with live detection boxes and a mint signal accent;
monospaced labels reinforce the "it's reading the card right now" moment.

- **Palette:** `#0D1014` bg · `#161B22` surface · `#37E1B4` mint accent · `#E9EDF2` ink · `#25D366` WhatsApp
- **Type:** monospaced labels + system sans body.
- **Best if** KIN wants to feel modern, precise, and speed-obsessed — a scanner people show off.

## Direction C — "Field"  ·  warm / human / relationship-first

Rounded type, soft cards, a friendly raspberry accent. Leans into the
"who is this person and how did we meet" story rather than the machinery.

- **Palette:** `#FCF7F6` warm white · `#2B2530` ink · `#D6336C` raspberry accent · `#FBEAF1` accent-soft · `#25D366` WhatsApp
- **Type:** rounded sans throughout.
- **Best if** KIN is consumer-friendly and personal — networking, community, warm follow-ups.

---

## Screens shown in every direction

1. **Capture** — camera with card-frame guide, live "detecting fields" feedback,
   and an **"Offline · saved locally / queued"** indicator (proves FR‑2.1,
   FR‑2.2, FR‑6.1).
2. **Review & edit** — parsed Name / Title / Company / Phone / Email as editable
   fields, **tag chips**, and a **"where did you meet?"** person note, then
   **Save to CRM** (FR‑2.3, FR‑2.4, FR‑3.4, FR‑4.2).
3. **Contact** — tags, a **follow-up reminder** card, the **person/relationship
   notes**, card fields, a **sync** status pill, and a **Share to WhatsApp**
   button (FR‑3.1, FR‑4.x, FR‑5.1, FR‑8.1).

## How to choose

- Pick primarily on **tone** (serious → A, technical → B, warm → C).
- **Mixing is allowed** — e.g. Ledger's structure with Field's warmth, or
  Signal's capture screen with Ledger's contact screen.
- The selected direction's color + type are lifted into a shared token set and
  applied to both the app and the CRM for a consistent system (NFR‑7.5).

> These are static, illustrative mockups (not the final pixel spec). Once a
> direction is chosen, the next step is a component library + a clickable
> prototype of the full flow.
