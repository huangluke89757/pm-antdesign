# Report & Dashboard UI Specification

> Binding rules for reports, monitoring dashboards, ledgers, and admin back-offices.
> Load this file whenever the target is a data-dense report. These rules override the general guidance in `SKILL.md`.
> Every rule is written as **must** (non-negotiable) or **should** (recommendation). Each must-rule carries an anti-pattern.
>
> Origin: distilled from an audit of two production report prototypes — a WMS inbound/outbound closure-monitoring report and a slow-moving-inventory expiry dashboard. Divergences between those two implementations are resolved here, once, so future reports do not re-litigate them.

---

## 1. Page Layout

### 1.1 Vertical hierarchy — fixed, five layers plus drill-down

```
L1  Page header      report name · data cut-off · 口径 entry · export action
L2  Filters          3-column grid · label 72px fixed · "clear" + result count echo
L3  KPI cards        5-way split · value 24px/600 · label + value + footnote
L4  Charts           optional · main:aux = 1.55 : 1 · card height ≥340px
L5  Main table       sticky header · 20 rows/page · numeric total row · pager
L6  Drill-down page  separate URL · breadcrumb bar · exportable
```

- **Must** keep the order L1 → L2 → L3 → L4 → L5. Filters must never sit below KPI cards: KPI values are derived from filters, so the reading order must match the data flow.
- **Must** use `16px` between layers (`12px` in compact mode) and `16px` between cards at the same layer.

**Anti-pattern:** placing KPI cards above the filter bar so users read numbers whose scope is undefined until they scroll back up.

### 1.2 Width and breakpoints

- **Must** choose one page width: `1280px` for single-table dashboards, `1760px` for reports with multiple charts or ≥12 columns. Centre it, with `24px` horizontal padding (`14px` below 768px).
- **Must** use exactly two breakpoints: `1199px` and `767px`.

| Width | L2 filters | L3 KPI | L4 charts | L5 table |
|---|---|---|---|---|
| ≥1200px | 3 columns `minmax(260px,1fr)` | 5 across | 1.55fr : 1fr | full width |
| 768–1199px | 2 columns | 3 across | stacked | horizontal scroll |
| <768px | 1 column | 1 across | stacked | horizontal scroll + sticky first column |

**Anti-pattern:** inventing a third breakpoint such as `1000px` or `1180px`. Each page then evolves its own intermediate layout and the suite stops being one system.

### 1.3 Mobile table behaviour

- **Must** keep tables as tables on mobile — horizontal scroll, never card-per-row.
- **Must** sticky the first column when columns ≥10.

**Rationale:** a report's value is cross-row comparison. Collapsing rows into cards destroys the column alignment that comparison depends on.

**Anti-pattern:** converting the table to a stack of cards below 768px, leaving users unable to compare values down a column.

### 1.4 Cards

- **Must** style cards as `#FFFFFF` background + `1px solid #E5E7EB` + `8px` radius + `0 1px 2px rgba(16,24,40,.04)` shadow + `16px 20px` padding.
- **Must** keep the page canvas at `#F0F2F5` so cards read as a distinct layer.
- **Should** avoid nesting cards more than one level; collapse filter groups beyond six items behind a "more filters" affordance.

**Anti-pattern:** a card whose background sits close to the canvas tone, erasing the layer separation and flattening the whole page.

---

## 2. Field Presentation

### 2.1 Column definition discipline

- **Must** give every column three things: a name, a unit, and a source field. Amount columns read `金额(元)`; quantity columns either carry a dedicated unit column or state the unit in the header.
- **Must** order columns by role: index → identifiers (code / document no. / batch) → classification (type / status / group) → subject (material / warehouse / org) → measures (quantity / amount / ratio / duration) → time → actions.
- **Must** declare an explicit `<colgroup>` at ≥8 columns, and set `table-layout:fixed` at ≥12 columns.
- **Must** cap the index column at `52px` with a fixed width.

**Anti-pattern:** letting the browser auto-size 12 columns so that column widths jump every time the data changes.

### 2.2 Numbers

- **Must** right-align every numeric column and apply `font-variant-numeric: tabular-nums`.
- **Must** limit quantities and amounts to 2 decimals, drop trailing `.00` on integers, and group thousands with `toLocaleString('zh-CN')`.
- **Must** abbreviate large amounts in KPI cards and chart axes only — `¥X.XX 亿` at ≥10^8, `¥X.X 万` at ≥10^4. **Table cells always show the exact value.**
- **Must** render ratios to 1 decimal with `%`, and durations to 1 decimal with `h`.

**Anti-pattern:** writing `¥4.88 亿` into a table cell. Finance cannot reconcile an abbreviated number; the abbreviation belongs on the summary card, not in the ledger.

### 2.3 Text and identifiers

- **Must** render document numbers, material codes, batch numbers, and timestamps in the mono font at `12px`, so shared prefixes line up and scan vertically.
- **Must** pass every user-controllable string through an HTML escape function before it enters the DOM.
- **Must** give long text columns a `max-width` plus ellipsis, and expose the full value via `title`.
- **Should** format timestamps as `YYYY-MM-DD HH:mm` and plain dates as `YYYY-MM-DD`. Do not mix `MM/DD` or `2026.08.24` into the same report.

**Anti-pattern:** interpolating a material name directly into `innerHTML` without escaping. One name containing `<script>` becomes stored XSS.

### 2.4 Status and grouping — badge, never plain text

- **Must** render every enumerated status (document status, line status, slow-moving group, closure state) as a pill badge: `height:20px; padding:0 10px; border-radius:999px; font-size:12px`, with background, text, and border all set.
- **Must** map badges to the five semantic tiers in `DESIGN.md` and never invent a colour. The same business meaning carries the same colour in every report.
- **Must** include the meaning in the badge text (`已过期`, `执行中`) — a colour swatch alone is not a label.

**Anti-pattern:** showing expiry state as a bare red dot. Colour-blind users and printed output lose the information entirely.

### 2.5 Ratios — colour plus symbol

- **Must** double-encode every key ratio: colour **and** a text or symbol anchor, e.g. `96.2% ↑达标` / `82.1% ↓低于阈值`.
- **Must** use four bands: `≥95%` success · `90–95%` info · `80–90%` warn · `<80%` danger. Business-specific thresholds are allowed only if documented in the header 口径 note.

**Anti-pattern:** distinguishing 96% from 82% purely by green versus red background fill.

### 2.6 Null and absent values

- **Must** render null as `—` coloured `#8C8F94`. Never use blank, `N/A`, `null`, or `0`.
- **Must** keep "value is zero" visually distinct from "value does not exist": zero renders `0`, not-applicable renders `—`.
- **Must** provide a real empty state when the whole table is empty (not merely filtered out), with `48px` vertical padding and a next-step hint.

**Anti-pattern:** rendering both a genuine zero and a missing field as `—`, making it impossible to tell "sold nothing" from "field not captured".

### 2.7 Totals row

- **Must** provide a `<tfoot>` total whenever the table carries quantity or amount columns, and the total **must** reflect the current filter result, not the full dataset.
- **Must** label the total with its scope, e.g. `合计（当前筛选）`.
- **Must not** sum ratio columns. Show a recomputed aggregate ratio, or leave the cell blank.

**Anti-pattern:** summing a closure-rate column into `170%`. Ratios are not additive; the total row must recompute from the underlying counts.

### 2.8 Row-level warning

- **Must** restrict row tint to rows needing action, in two levels only: `#FFF8E8` (attention) and `#FFF0F0` (severe).
- **Must** pair every tinted row with a badge or a bold coloured value — the tint may never be the row's only signal.
- **Must** let row hover override the warning tint so hover feedback stays visible.

**Anti-pattern:** tinting a row red and leaving every cell in normal-weight black text, so the row carries no information once printed in greyscale.

### 2.9 Metric definitions must be traceable

- **Must** expose the definition of every computed field — formula, scope, and data cut-off — through an info affordance or a header tooltip.
- **Must** show a data cut-off timestamp in the header as `数据截至 YYYY-MM-DD HH:mm`, with a freshness dot: green ≤1h, amber ≤24h, red >24h.

**Anti-pattern:** shipping a `平均闭环时长` column with no statement of whether it averages closed documents only, all documents, or documents created within the filter range.

### 2.10 KPI cards

- **Must** structure every KPI card as three parts: label (`12px`, `#5F6368`), value (`24px`/600, tabular numerals), footnote (`12px`, `#8C8F94`, stating numerator/denominator or sample size).
- **Must** recompute KPI values against the active filters, and name the scope in the label (`库存总金额（当前筛选）`).
- **Must** give clickable KPI cards three affordances: hover lift with a primary border and a `查看明细 →` hint, `cursor:pointer`, and `Enter`-key activation.

**Anti-pattern:** a KPI card labelled just `库存总金额` that silently reflects an active filter, so a user reports "the warehouse lost 400 million" when they had in fact filtered to one group.

---

## 3. Interaction

### 3.1 Filter controls

| Control | Use when | Requirements |
|---|---|---|
| `<select>` | enumerations ≤15 values | first option is `全部X` with an empty value; custom arrow via `appearance:none`; `32px` height; `min-width:120px` |
| `<datalist>` searchable input | object dimensions >15 values | filter as the user types; on no match, revert to the previously selected value rather than clearing |
| Multi-select popover | multiple objects at once | trigger label reflects selection (`全部X` / value / `已选 N 个`); closes on outside click; panel scrolls beyond `260px` |
| Chip group | weak-classification multi-select | unselected: white with a neutral border; selected: tier background + tier border + tier text |
| Cascading selects | dimensions with a natural hierarchy (name → spec → code) | changing an upstream control **must** clear and recompute downstream options |
| Keyword search | cross-field fuzzy match | placeholder states the searched fields; debounce ≥200ms |

- **Must** reset pagination to page 1 on every filter change.
- **Must** provide a "clear filters" action that resets controls, chips, popover checkboxes, page number, and persisted state together.
- **Must** echo the active filter set in readable form, e.g. `筛选：仓库×2 + 分组×1 · 命中 12 条`, or `未启用筛选 · 显示全部 N 条` when none are active.
- **Must** preserve filter conditions when they yield zero rows, offering a clear action instead of auto-resetting.
- **Should** collapse filter groups beyond six items.

**Anti-pattern:** auto-clearing all filters the moment a combination returns zero rows. The user loses the context they were narrowing toward.

### 3.2 Sorting

- **Must** make numeric, date, and ratio columns sortable. Text columns optional.
- **Must** show three sort states with `▲`/`▼` and set `aria-sort` on the header cell.
- **Must** default the first click on a numeric column to descending and on a text column to ascending.
- **Must** default the whole table to the most business-relevant measure, descending — never to primary key or insertion order.
- **Must** sort Chinese text with `localeCompare(b, 'zh-CN')` and treat `null` without throwing.
- **Must** sort the full filtered result, not just the current page.

**Anti-pattern:** sorting only the 20 rows on screen, so page 2 appears to violate the sort order.

### 3.3 Pagination

- **Must** default to 20 rows per page, with 20/50/100 options.
- **Must** use a windowed pager: first page, last page, current ±1, with `…` for the gaps; disable prev/next at the boundaries at `opacity:.4`.
- **Must** display `共 N 条 · 第 X / Y 页` beneath the table, count on the left and pager on the right.
- **Must** clamp an out-of-range page to the last valid page rather than rendering a blank table.
- **Should** scroll the table back to its top after a page change.

**Anti-pattern:** filtering down to 3 pages while the user sits on page 9, leaving them staring at an empty table.

### 3.4 Drill-down

- **Must** carry drill-down conditions in URL query parameters so the view survives refresh, sharing, and back navigation. Fixed names: `warehouse`, `type`, `range`, `group`, `page`.
- **Must** put a return bar at the top of the detail page — a `← 返回主看板` button plus a note that conditions were inherited.
- **Must** render drillable numbers as `<button>` elements styled with the primary colour and a hover underline, never as `<span onclick>`.
- **Must** let the detail page adjust its own filters without mutating the main dashboard's state, and let it export independently.
- **Should** navigate in the same tab to avoid popup blocking; if opening a new window, detect a `null` return from `window.open` and tell the user popups are blocked.

**Anti-pattern:** storing drill-down state only in memory, so a refresh or a shared link dumps the recipient on the unfiltered dashboard.

### 3.5 Charts

- **Must** give every chart a title stating what it shows, not merely `趋势图`.
- **Must** provide a legend and hover-linked emphasis on multi-series charts: hovering one series drops the others to `opacity:.1` and thickens the active stroke.
- **Must** assign series colours by the fixed sequence — series N takes colour N — and keep an entity's colour stable across every chart on the page.
- **Must** give line charts a tooltip (dark `rgba(19,27,41,.96)`, white text, `7px` radius) plus a crosshair.
- **Must** clamp tooltip position so it never leaves the viewport: `left = min(innerWidth - tipWidth - 12, clientX + 14)`, `top = max(8, min(innerHeight - tipHeight - 12, clientY - 12))`.
- **Must** present donut/pie legends beyond six series as a sorted value list rather than colour dots alone, with two-way highlight against the segments.
- **Must** render a fallback ring or empty-state message when chart data is empty — never an empty axis frame.
- **Should** skip entry animation; use `150ms` for interaction feedback only.

**Anti-pattern:** a seven-series line chart with no hover linkage, where every series stays at full opacity and the reader cannot follow any single line.

### 3.6 Export

- **Must** name exported files `{scope}_{report}_{YYYYMMDD}.xlsx`, e.g. `上海仓库_未关闭出入库单据明细_20260824.xlsx`.
- **Must** export the full filtered set, not the current page, with columns matching the on-screen columns.
- **Must** disable the export button and change its label to `导出中…` while running.
- **Should** write numeric columns as numeric cell types so Excel can sum them directly.

**Anti-pattern:** exporting only the visible 20 rows, so the file silently disagrees with the `共 1,247 条` count shown on screen.

### 3.7 Feedback timing

| Action | Feedback | Threshold |
|---|---|---|
| Hover / focus | primary border + 2px ring | immediate, `150ms` transition |
| Recompute (filter / sort / page) | inline skeleton or body `opacity:.6` | silent ≤100ms; spinner 100ms–1s; skeleton >1s |
| Export | button disabled + label change | immediate |
| Global refresh | timestamp spinner + updated cut-off | immediate |

### 3.8 Three states are mandatory

- **Must** implement loading, empty, and error states for every data region. Missing any one means the report is incomplete.
- **Must** render loading as a table skeleton (rows matching page size) with KPI values showing `—`; never block the whole page behind a full-screen overlay.
- **Must** split empty states into two messages: no data at all (`暂无数据` plus the refresh cadence) versus filtered-to-nothing (`当前筛选条件下无匹配记录` plus a clear action).
- **Must** make the error state degrade locally: a failed column shows `—` with an `ⓘ` header note reading "该字段暂不可用", while the rest of the report renders normally.

**Anti-pattern:** implementing only the happy path. The prototype looks perfect against hardcoded data and white-screens the first day it meets a real API.

### 3.9 State persistence

- **Must** persist filters, sorting, and pagination across a page refresh — URL query parameters preferred, `sessionStorage` acceptable.
- **Must** clear persisted state and URL parameters when the user clears filters.
- **Should** preserve sorting and pagination across a drill-down round trip.

**Anti-pattern:** keeping filter state in a plain in-memory object, so every refresh silently resets the user's narrowing work.

---

## 4. Accessibility

- **Must** make every interactive element keyboard reachable using native `<button>`, `<a>`, `<select>`, `<input>`. Never bind `onclick` to a `div`/`span` without `tabindex="0"` plus `Enter`/`Space` handling.
- **Must** render a visible focus indicator: `:focus-visible { outline:2px solid var(--color-primary); outline-offset:2px }`. Never write `outline:none` without a replacement.
- **Must** keep tab order matching visual order, with no focus traps.
- **Must** let multi-select popovers close on `Esc` and return focus to the trigger.
- **Must** mark up tables with `<thead>`/`<tbody>`/`<tfoot>`, `scope="col"` on headers, and a `<caption>` (visually hidden is fine).
- **Must** set `aria-sort` on sortable headers and update it on click.
- **Must** give SVG charts `role="img"` with an `aria-label` describing the trend and key values, or provide a visually hidden data-table alternative.
- **Must** announce filter results through an `aria-live="polite"` region.
- **Must** give icon-only buttons an `aria-label` and mark decorative elements `aria-hidden="true"`.
- **Should** size icon hit areas at ≥24×24px (≥44×44px for touch).

**Anti-pattern:** `outline:none` on inputs with no replacement focus style. Keyboard users lose their position entirely in a form-heavy report.

---

## 5. Implementation Constraints

- **Must** deliver as a single self-contained HTML file (an accompanying `design-tokens.css` is acceptable). No CDN, no chart library, no web font — the file must open by double-click in an offline environment.
- **Must** centralise data in one place (`const XXX_DATA = {…}`) so swapping in an API touches one line.
- **Must** escape all user-controllable strings before DOM insertion.
- **Must** render with pure functions that take state and return an HTML string, assigning `innerHTML` wholesale. Never `innerHTML +=` to append rows — it re-binds events and leaks handlers.
- **Must** wrap page logic in an IIFE or `<script type="module">`.
- **Must** declare `<meta name="color-scheme" content="light">`; reports use a light baseline.
- **Should** use `position:sticky` for the header and first column of wide tables rather than relying on an outer scroll container.
- **Should** hand-write OOXML for export rather than pulling in a spreadsheet library.

---

## 6. Anti-Pattern Library

| # | Prohibited | Do instead | Rule |
|---|---|---|---|
| 1 | `outline:none` with no replacement | `:focus-visible` with a 2px primary ring | §4 |
| 2 | Font weights 550 / 650 | 400 / 500 / 600 only | DESIGN.md |
| 3 | 10px or 12px card radius | 8px | DESIGN.md |
| 4 | KPI value at 27px, page title at 21px | 24px and 20px | DESIGN.md |
| 5 | Canvas `#f4f6f9`, header `#f8fafc` | `#F0F2F5`, `#FAFAFA` | DESIGN.md |
| 6 | Status conveyed by colour alone | Badge with text + tier triple | §2.4 |
| 7 | Ratio shown only as green/red fill | `96% ↑达标` / `82% ↓低于阈值` | §2.5 |
| 8 | Null rendered as blank / `N/A` / `null` | `—` in `#8C8F94` | §2.6 |
| 9 | Zero and absent both rendered `—` | `0` versus `—` | §2.6 |
| 10 | Summing ratio columns | Recompute or leave blank | §2.7 |
| 11 | Total over full dataset while filtered | Total tracks the filter, labelled with scope | §2.7 |
| 12 | Page number not reset after filtering | Reset to page 1 | §3.1 |
| 13 | Auto-clearing filters at zero results | Keep them, offer a clear action | §3.1 |
| 14 | Only empty state implemented | All three states, error degrades locally | §3.8 |
| 15 | `innerHTML +=` to append rows | Wholesale re-render | §5 |
| 16 | Raw `color:#1769e0` in a component | `var(--color-primary)` | DESIGN.md |
| 17 | Filter state in memory only | URL params or `sessionStorage` | §3.9 |
| 18 | `¥4.88 亿` inside a table cell | Exact value in cells; abbreviate in KPI/axes | §2.2 |
| 19 | Component widths jumping as data loads | `<colgroup>` + `table-layout:fixed` | §2.1 |
| 20 | A third responsive breakpoint | Only `1199px` and `767px` | §1.2 |
