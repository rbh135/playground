# KIN — Design Tokens (Direction B · "Signal")

The selected identity is **Direction B — "Signal"**: dark, camera‑first,
technical. These tokens are the single source of truth for color, type,
spacing, and radius across **both** the mobile app and the Next.js CRM
(NFR‑7.5). Preview: https://claude.ai/code/artifact/7d56e76b-843a-40c2-93f8-f947b78f414a

## Color

| Token | Hex | Use |
|---|---|---|
| `--bg` | `#0D1014` | App background (base) |
| `--surface` | `#161B22` | Cards, sheets, list rows |
| `--elevated` | `#1E2531` | Raised surfaces, menus |
| `--line` | `#262E3A` | Borders, dividers |
| `--ink` | `#E9EDF2` | Primary text |
| `--muted` | `#8A94A3` | Secondary text, labels |
| `--accent` | `#37E1B4` | Primary accent (mint "signal") — CTAs, active state, detection |
| `--accent-soft` | `rgba(55,225,180,.14)` | Accent tints, chips, highlighted cards |
| `--accent-ink` | `#04241B` | Text/icon on top of the accent fill |
| `--whatsapp` | `#25D366` | WhatsApp share action **only** |

**Semantic (state) colors — separate from the accent:**

| Token | Hex | Meaning |
|---|---|---|
| `--success` | `#37D399` | Synced / done |
| `--warning` | `#F2C14E` | Queued / due soon |
| `--danger` | `#F2685C` | Sync failed / overdue |
| `--info` | `#5AB0F0` | Neutral info |

> The accent (mint) signals **interactivity and recognition**, not status. Keep
> WhatsApp green reserved for the WhatsApp action so it never reads as a generic
> success color.

### Light mode (CRM web)

The web CRM should also offer a light theme derived from the same hues so the
system feels unified in daylight/office use:

| Token | Hex |
|---|---|
| `--bg` | `#F4F6F8` |
| `--surface` | `#FFFFFF` |
| `--line` | `#E2E7EC` |
| `--ink` | `#131720` |
| `--muted` | `#5A6472` |
| `--accent` | `#0F9E7C` *(darkened mint for contrast on light)* |
| `--accent-soft` | `#E2F7F0` |

## Typography

| Role | Stack | Notes |
|---|---|---|
| **Body / UI** | `system-ui, -apple-system, "Segoe UI", Roboto, sans-serif` | Default text. |
| **Labels / data** | `ui-monospace, "SF Mono", Menlo, "Cascadia Code", monospace` | Field labels, IDs, statuses, "detecting…" — the technical voice of Signal. |
| **Numerics** | add `font-variant-numeric: tabular-nums` | Any aligned digits (counts, dates, phone). |

**Type scale (px):** 11 · 12 · 13 · 15 · 17 · 20 · 24 · 30
**Label style:** uppercase, `letter-spacing: .12–.16em`, `--muted`.

## Shape & spacing

| Token | Value |
|---|---|
| `--radius-sm` | 10px |
| `--radius-md` | 14px (cards) |
| `--radius-lg` | 20px (sheets) |
| `--radius-pill` | 999px |
| Spacing scale | 4 · 8 · 12 · 16 · 20 · 24 · 32 |
| Card border | `1px solid var(--line)` |

## Signature UI moments (from the chosen mockups)

- **Scan screen:** dark viewfinder, faint mint grid, corner brackets, and
  animated **detection boxes** that resolve into `name ✓ / phone ✓ / email ✓`
  chips as fields are recognized on‑device.
- **Offline pill:** monospace `◦ OFFLINE · QUEUED` in the accent, top‑left.
- **Shutter:** hollow mint ring with a solid mint core and a soft glow.
- **Review:** monospace field labels (`name`, `company`, `phone`) with a
  confidence chip; primary CTA reads `Commit to CRM →`.
- **Contact:** accent‑tinted **follow‑up** card, `about_person` notes, `● synced`
  chip, and the reserved‑green **Share to WhatsApp** button.

## CSS custom-property starter

```css
:root{
  --bg:#0D1014; --surface:#161B22; --elevated:#1E2531; --line:#262E3A;
  --ink:#E9EDF2; --muted:#8A94A3;
  --accent:#37E1B4; --accent-soft:rgba(55,225,180,.14); --accent-ink:#04241B;
  --whatsapp:#25D366;
  --success:#37D399; --warning:#F2C14E; --danger:#F2685C; --info:#5AB0F0;
  --radius-sm:10px; --radius-md:14px; --radius-lg:20px; --radius-pill:999px;
  --font-ui:system-ui,-apple-system,"Segoe UI",Roboto,sans-serif;
  --font-mono:ui-monospace,"SF Mono",Menlo,"Cascadia Code",monospace;
}
```
