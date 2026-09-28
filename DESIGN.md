---
name: Ant
version: "2.0"
colors:
  primary: "#1677ff"
  primaryHover: "#4096ff"
  primaryActive: "#0958d9"
  primarySubtle: "#E6F1FB"
  text1: "#111827"
  text2: "#5F6368"
  text3: "#8C8F94"
  border1: "#E5E7EB"
  border2: "#F0F0F0"
  borderControl: "#D8DEE9"
  bgPage: "#F0F2F5"
  bgSurface: "#FFFFFF"
  bgThead: "#FAFAFA"
  bgRowHover: "#F7F9FC"
  dangerBg: "#FCEBEB"
  dangerTx: "#A32D2D"
  dangerBd: "#F09595"
  warnBg: "#FAEEDA"
  warnTx: "#854F0B"
  warnBd: "#EF9F27"
  infoBg: "#E6F1FB"
  infoTx: "#185FA5"
  infoBd: "#85B7EB"
  successBg: "#E8F7F1"
  successTx: "#0D7A50"
  successBd: "#8FD9BB"
  neutralBg: "#F0F2F5"
  neutralTx: "#5D6675"
  neutralBd: "#D8DEE9"
  rowWarnBg: "#FFF8E8"
  rowDangerBg: "#FFF0F0"
typography:
  h1: { fontFamily: "Plus Jakarta Sans", fontSize: "20px", fontWeight: 600 }
  h3: { fontFamily: "Plus Jakarta Sans", fontSize: "16px", fontWeight: 600 }
  kpi: { fontFamily: "Plus Jakarta Sans", fontSize: "24px", fontWeight: 600 }
  body: { fontFamily: "Plus Jakarta Sans", fontSize: "14px", fontWeight: 400 }
  table: { fontFamily: "Plus Jakarta Sans", fontSize: "13px", fontWeight: 400 }
  label: { fontFamily: "Plus Jakarta Sans", fontSize: "12px", fontWeight: 500 }
  mono: { fontFamily: "JetBrains Mono", fontSize: "12px" }
  scale: "12/13/14/16/20/24/28"
  weights: "400, 500, 600"
rounded:
  xs: 4px
  sm: 6px
  md: 8px
  full: 999px
spacing:
  scale: "4/8/12/16/24/32"
  page-padding: 24px
  card-gap: 16px
  card-padding: "16px 20px"
sizing:
  control: 32px
  controlLg: 36px
  pagerBtn: 28px
  badge: 20px
  pageMaxStd: 1280px
  pageMaxWide: 1760px
series:
  - "#1677ff"
  - "#16A34A"
  - "#D97706"
  - "#DC2626"
  - "#8B5CF6"
  - "#0DA5A5"
  - "#D84F9B"
  - "#5F7DDE"
  - "#E66B2E"
  - "#9164DA"
  - "#6A9C21"
  - "#2C90C8"
---

## Overview

Structured, enterprise-focused design system emphasizing clarity, consistency, and efficiency for data-dense web applications: reports, monitoring dashboards, ledgers, and admin back-offices.

## Style Foundations

- **Visual style:** data-dense, enterprise
- **Typography scale:** 12/13/14/16/20/24/28 — closed set of 7 steps
- **Typography fonts:** primary = Plus Jakarta Sans, display = Plus Jakarta Sans, mono = JetBrains Mono
- **Typography weights:** 400, 500, 600 — closed set of 3
- **Color roles:** primary, neutral, success, warning, danger
- **Spacing scale:** 4/8/12/16/24/32 — closed set of 6 steps
- **Radius:** 4 (xs) / 6 (control) / 8 (card) / 999 (pill) — closed set

## Colors

### Primary

| Token | Value | Use |
|---|---|---|
| `--color-primary` | `#1677ff` | Primary buttons, links, clickable numbers, selected borders. Exactly one primary per page. |
| `--color-primary-hover` | `#4096ff` | Hover state of primary surfaces |
| `--color-primary-active` | `#0958d9` | Pressed state |
| `--color-primary-subtle` | `#E6F1FB` | Selected/active backgrounds, subtle tint |

`#1677ff` matches the Ant Design v5 default `colorPrimary`.

### Text (three levels)

| Token | Value | Use |
|---|---|---|
| `--text-1` | `#111827` | Body text, table cell content, values |
| `--text-2` | `#5F6368` | Labels, table headers, captions, secondary descriptions |
| `--text-3` | `#8C8F94` | Tertiary info, disabled state, null-value symbol `—` |

Never place a tertiary text color on a tinted background without checking contrast — that pairing is where reports most often fail WCAG AA.

### Surface

| Token | Value | Use |
|---|---|---|
| `--bg-page` | `#F0F2F5` | Page canvas — the layer behind cards |
| `--bg-surface` | `#FFFFFF` | Card and panel surfaces |
| `--bg-thead` | `#FAFAFA` | Table header and total row |
| `--bg-row-hover` | `#F7F9FC` | Row hover — takes precedence over row warning tint |

Canvas-versus-surface contrast is what creates hierarchy. A card whose background approaches the canvas color erases the layer structure.

### Border

| Token | Value | Use |
|---|---|---|
| `--border-1` | `#E5E7EB` | Card outlines, cell dividers |
| `--border-2` | `#F0F0F0` | Inner dividers within a component |
| `--border-control` | `#D8DEE9` | Input, select, and button outlines |

### Semantic — five tiers

Each tier ships as a **bg + text + border triple**. Use the triple as a set; never borrow a background from one tier and a text color from another.

| Tier | Background | Text | Border | Meaning examples |
|---|---|---|---|---|
| danger | `#FCEBEB` | `#A32D2D` | `#F09595` | Expired · severe overtime (>48h) · closure rate <80% · unclosed backlog |
| warn | `#FAEEDA` | `#854F0B` | `#EF9F27` | Near-expiry (≤3 months) · overtime (24–48h) · closure rate 80–90% |
| info | `#E6F1FB` | `#185FA5` | `#85B7EB` | Early warning (3–9 months) · in progress · closure rate 90–95% |
| success | `#E8F7F1` | `#0D7A50` | `#8FD9BB` | Normal · completed · closed · closure rate ≥95% |
| neutral | `#F0F2F5` | `#5D6675` | `#D8DEE9` | Pending · not started · no status · disabled |

### Row warning tint

| Token | Value | Use |
|---|---|---|
| `--row-warn-bg` | `#FFF8E8` | Rows needing attention |
| `--row-danger-bg` | `#FFF0F0` | Rows needing immediate action |

Two levels only. A row tint may never be the sole signal in that row — pair it with a badge or a bold colored value.

### Chart series — fixed 12-color sequence

`#1677ff` · `#16A34A` · `#D97706` · `#DC2626` · `#8B5CF6` · `#0DA5A5` · `#D84F9B` · `#5F7DDE` · `#E66B2E` · `#9164DA` · `#6A9C21` · `#2C90C8`

Series N always takes color N. The same entity must carry the same color across every chart on the page.

`#8B5CF6` (series 5) is the only violet in the set. It exists for the 5th chart series only — do not promote it to a UI accent color, as it conflicts with the data-dense enterprise tone.

## Typography

| Role | Size | Weight | Use |
|---|---|---|---|
| h1 | 20px | 600 | Page title |
| h3 | 16px | 600 | Card / section title |
| kpi | 24px | 600 | KPI value |
| body | 14px | 400 | Page baseline |
| table | 13px | 400 | Table cells, controls |
| label | 12px | 500 | Table headers, labels, captions, badges |
| mono | 12px | 400 | Document IDs, material codes, batch numbers, timestamps |

All numeric content must set `font-variant-numeric: tabular-nums`.

Weights 400/500/600 only. Non-standard weights such as 550 or 650 render inconsistently across platforms — do not use them.

## Spacing

Scale: 4 / 8 / 12 / 16 / 24 / 32. No other values.

| Context | Value |
|---|---|
| Page horizontal padding | 24px (14px below 768px) |
| Gap between layers / cards | 16px (12px compact) |
| Card inner padding | 16px 20px |
| KPI grid gap | 12px |
| Table cell padding | 9px 12px |

## Radius

| Token | Value | Use |
|---|---|---|
| `--r-xs` | 4px | Tiny elements, inner chips |
| `--r-sm` | 6px | Controls: input, select, button, pager |
| `--r-md` | 8px | Cards, panels, dropdowns |
| `--r-full` | 999px | Pill badges, chips |

## Shadow

| Token | Value | Use |
|---|---|---|
| card | `0 1px 2px rgba(16,24,40,.04)` | Cards — extremely subtle |
| popover | `0 6px 16px rgba(0,0,0,.08)` | Dropdown panels |
| tooltip | `0 7px 24px rgba(0,0,0,.2)` | Dark tooltips |

No shadows on data rows, buttons, or badges.

## Sizing

| Token | Value | Use |
|---|---|---|
| Control height | 32px | Input, select, button (36px allowed in page-header filters) |
| Pager button | 28px | Pagination |
| Badge height | 20px | Status badges |
| Page max-width | 1280px | Single-table dashboards |
| Page max-width (wide) | 1760px | Reports with multiple charts or ≥12 columns |

## Motion

Transition duration is fixed at `150ms ease`, applied only to `background`, `border-color`, `opacity`, `transform`, `box-shadow`, and `stroke-width`. Decorative animation is prohibited — motion in a data view exists solely to make state changes perceptible. Honor `prefers-reduced-motion: reduce` by disabling all transitions.

## Responsive Breakpoints

Two breakpoints only: `1199px` and `767px`. Do not introduce intermediate breakpoints such as 1000px or 1180px — they fragment the system and produce layouts no other page in the suite shares.

## Tokens vs. Raw Values

The token set is the contract. A page that hardcodes `#1769e0` where the system specifies `#1677ff` has forked the design system, and every subsequent page inherits the drift. Audit for raw values before review: search the stylesheet for `#` outside the token block, and for pixel values outside the six-step spacing scale.
