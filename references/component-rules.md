# Component Rules

> Anatomy, states, interaction behaviour, and accessibility acceptance criteria for the components a data-dense report is built from.
> Load this file when specifying component-level detail or reviewing component implementations.

Every component lists: **anatomy** → **required states** → **interaction** → **token usage** → **edge cases** → **acceptance criteria**.

---

## Table

### Anatomy

```
<table>
  <caption>                      <!-- visually hidden; states the report name -->
  <colgroup>                     <!-- mandatory at ≥8 columns -->
  <thead> <th scope="col">       <!-- sticky, aria-sort on sortable ones -->
  <tbody> <td>
  <tfoot>                        <!-- total row, present whenever numeric columns exist -->
```

### Required states

| State | Treatment |
|---|---|
| default | `padding:9px 12px`, `border-bottom:1px solid var(--border-2)` |
| hover | row background `var(--bg-row-hover)`; overrides row warning tint |
| selected | `var(--color-primary-subtle)` background |
| warning rows | `var(--row-warn-bg)` / `var(--row-danger-bg)`, always with a badge or bold value |
| empty | centred message, `48px` vertical padding, `var(--text-3)` |
| loading | skeleton rows matching the page size |

### Header rules

- Sticky via `position:sticky; top:0; z-index:2`.
- Background `var(--bg-thead)`, text `var(--text-2)`, size `var(--fs-cap)`, weight `var(--fw-medium)`.
- Sortable headers get `cursor:pointer`, a `▲`/`▼` indicator, and `aria-sort`.
- Header labels never wrap; the column scrolls instead.

### Edge cases

- Long cell text: `max-width` + ellipsis + `title`.
- Wide tables: sticky first column at ≥10 columns.
- Missing column data: render `—`, add an `ⓘ` header note.
- Very long table: paginate, and clamp an out-of-range page.

### Acceptance criteria

- [ ] Header stays pinned during vertical scroll.
- [ ] Column widths do not change as data loads.
- [ ] Every numeric column is right-aligned with `tabular-nums`.
- [ ] `scope="col"` on all header cells; a `<caption>` exists.
- [ ] `aria-sort` updates on sort.
- [ ] Total row recomputes against the active filter.

---

## KPI Card

### Anatomy

```
┌─────────────────────────┐
│ Label          12px     │   var(--text-2)
│ 24px value              │   var(--text-1), weight 600, tabular-nums
│ footnote       12px     │   var(--text-3)
└─────────────────────────┘
```

### Required states

| State | Treatment |
|---|---|
| default | white surface, `1px solid var(--border-1)`, `8px` radius, `16px` padding, min-height `104px` |
| clickable | `cursor:pointer`; hover lifts `-2px`, border goes primary, `查看明细 →` fades in |
| filtered-dimmed | `opacity:.4` when a sibling filter group is active and this card is not selected |
| loading | value shows `—` with a subtle shimmer |

### Interaction

- `Enter` and `Space` activate a clickable card.
- Clickable cards are `<button>` elements, not `<div>`.
- Hover hint text is decorative and `aria-hidden`; the accessible name comes from the label.

### Token usage

Label `12px`/`var(--text-2)` · value `24px`/600/`var(--text-1)` · footnote `12px`/`var(--text-3)`. Semantic variants use the tier triple as background, border, and text.

### Edge cases

- Value wider than the card: reduce to `--fs-h1`, never truncate a number.
- Footnote longer than one line: clamp to one line with a `title`.
- A card with no data: value `—`, footnote explains why.

### Acceptance criteria

- [ ] Exactly three parts present: label, value, footnote.
- [ ] Value is tabular-numeric and right-comparable with siblings.
- [ ] Scope is named in the label when the card reflects active filters.
- [ ] Clickable cards respond to `Enter`.
- [ ] Card height ≥ `104px`, and all cards in the row match.

---

## Status Badge

### Anatomy

Pill: `height:20px`, `padding:0 10px`, `border-radius:999px`, `font-size:12px`, `font-weight:500`, with background + text + border from one semantic tier.

### Variants

Five tiers, mapped in `DESIGN.md`. A tier's three values are used as a set.

### Edge cases

- Badge text longer than ~6 characters: shorten the label rather than widening the pill.
- Many badges in one cell: cap at 3, then `+N` with a `title` listing the rest.

### Acceptance criteria

- [ ] Background, text, and border all come from the same tier.
- [ ] Badge text states the meaning without relying on colour.
- [ ] Contrast of tier text against tier background ≥ 4.5:1.
- [ ] Identical business meanings use identical tiers across every report.

---

## Filter Control

### Anatomy

`label (72px fixed) + control (flex 1)` inside a 3-column grid.

### Required states

| State | Treatment |
|---|---|
| default | `1px solid var(--border-control)`, `var(--r-sm)`, `32px` height |
| hover | border → `var(--color-primary)` |
| focus | border primary + `0 0 0 2px rgba(22,119,255,.12)` ring |
| disabled | `opacity:.4`, `cursor:not-allowed` |
| invalid | border → `var(--state-danger-bd)` |

### Interaction

- Tab order follows the visual grid, row by row.
- Changing a control resets pagination to page 1.
- Cascading selects clear downstream values and recompute their options.
- `<datalist>` inputs revert to the last valid value on unmatched entry.

### Edge cases

- More than 6 filters: collapse the tail behind "more filters".
- Narrow viewport: grid collapses 3 → 2 → 1 column.
- No matching option: keep the previous selection rather than blanking.

### Acceptance criteria

- [ ] All controls share one height per row.
- [ ] Focus ring visible on every control.
- [ ] Cascades clear downstream values.
- [ ] Filter change resets to page 1.
- [ ] "Clear filters" resets every control, chips, popover state, page, and URL.

---

## Pagination

### Anatomy

`‹ [1] [2] [3] … [12] [›]` — windowed, with the current page filled with the primary colour.

### Required states

| State | Treatment |
|---|---|
| default | `var(--h-pagebtn)` square, `1px solid var(--border-control)`, `var(--r-sm)` |
| hover | border and text → primary |
| current | primary fill, white text |
| disabled | `opacity:.4`, `cursor:not-allowed` |

### Interaction

- Boundary buttons disable rather than disappearing.
- Clicking a page scrolls the table back to its top.
- Changing page size resets to page 1.

### Acceptance criteria

- [ ] Window shows first, last, and current ±1 with `…` gaps.
- [ ] Count text `共 N 条 · 第 X / Y 页` present.
- [ ] Out-of-range pages clamp to the last valid page.
- [ ] Buttons are keyboard reachable with a visible focus ring.

---

## Chart (line / donut)

### Anatomy

```
Card
├─ Title (16px/600)            states what the chart shows
├─ Series toggle or range seg  optional
├─ Legend                      dot + label, hover-linked
└─ Plot area (≥270px)          inline SVG, viewBox-scaled
```

### Required states

| State | Treatment |
|---|---|
| default | all series at full opacity |
| focus (one series hovered) | active stroke `3.5`, others `opacity:.1` |
| empty | grey fallback ring or empty-state message |
| loading | skeleton plot area |

### Interaction

- Hovering a legend entry emphasises its series.
- Line charts show a crosshair plus a dark tooltip.
- Tooltip position clamps to the viewport.
- Mouse-out restores all series and hides the crosshair.

### Token usage

Series colours come from the fixed 12-colour sequence. Grid lines `#E8EDF4`, axis labels `var(--fs-cap)` in `var(--text-3)`.

### Edge cases

- >6 donut segments: switch the legend to a sorted value list.
- Sparse data: still draw points, not just a line.
- A series with no data in the range: break the line, do not connect across the gap.

### Acceptance criteria

- [ ] Chart has a descriptive title.
- [ ] `role="img"` with an `aria-label`, or a hidden data-table alternative.
- [ ] Series colours follow the fixed sequence and stay stable across charts.
- [ ] Tooltip never leaves the viewport.
- [ ] Empty data renders a fallback, not an empty axis frame.

---

## Drill-down Detail Page

### Anatomy

```
Back bar      ← 返回主看板 · "筛选条件已由主看板带入"
Header        page title · scope subtitle · export button
Filters       own filter set, independent of the main dashboard
Table         full column set, paginated, exportable
```

### Interaction

- Conditions arrive via URL params and are read on load.
- The detail page's own filters never mutate the main dashboard's state.
- Returning restores the main dashboard exactly as left.

### Acceptance criteria

- [ ] Conditions live in URL params and survive refresh.
- [ ] Back bar present with an inherited-condition note.
- [ ] Detail filters and export work independently.
- [ ] Returning does not alter the main dashboard's filters.

---

## Empty / Loading / Error States

### Anatomy

| State | Content |
|---|---|
| loading | table skeleton matching page size; KPI values `—` |
| empty (no data) | `暂无数据` + refresh-cadence hint |
| empty (filtered out) | `当前筛选条件下无匹配记录` + clear-filters action |
| error | short cause + `重新加载` action |
| partial error | failed column renders `—` with an `ⓘ` header note |

### Acceptance criteria

- [ ] All three states exist for every data region.
- [ ] Loading does not block the whole page.
- [ ] The two empty variants carry different copy.
- [ ] A partial failure degrades the affected column only.
- [ ] The error state offers a retry action.
