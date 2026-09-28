# QA Checklist

> Executable during code review or a live audit. Every item must yield "pass / fail **with evidence**".
> This skill carries no scripts, so this checklist is the substitute for automated validation. Run it item by item; do not accept a verbal assurance in place of observed behaviour.

---

## A. Visual Consistency

Run in DevTools on the rendered page.

- [ ] Search the stylesheet for `#` outside the token block → **zero** raw hex values.
- [ ] Search for spacing values outside `4/8/12/16/24/32` → **zero** (pager buttons excepted).
- [ ] Search for font sizes outside `12/13/14/16/20/24/28` → **zero**.
- [ ] Search for font weights outside `400/500/600` → **zero**.
- [ ] Search for radii outside `4/6/8/999px` → **zero**.
- [ ] Sample three text-on-background pairs through a contrast checker → all ≥ `4.5:1`.
- [ ] Count distinct semantic tier combinations on the page → ≤ 5.
- [ ] Every numeric column right-aligned with visually aligned digits (zoom to verify).
- [ ] Every card uses radius 8, border `#E5E7EB`, and the `0 1px 2px` shadow.
- [ ] Canvas is `#F0F2F5` and surfaces are `#FFFFFF`.
- [ ] Page renders correctly at 1200px, 900px, and 375px with no unintended overflow.

**Fail examples:** a component with `color:#1769e0`; a card at `border-radius:10px`; a title at `font-weight:650`.

---

## B. Fields and Data

Check against real data, not the prototype fixture.

- [ ] Every numeric column declares its unit — in the header or via a dedicated unit column.
- [ ] Total row equals the column sum of the **current filter result** (hand-verify one column).
- [ ] Ratio columns are not summed in the total row.
- [ ] Zero and null are visually distinguishable.
- [ ] The longest material name does not break the column, and `title` reveals the full value.
- [ ] Injecting `<script>alert(1)</script>` as a material name renders as literal text, unexecuted.
- [ ] Header shows a data cut-off timestamp.
- [ ] Every computed field exposes its formula, scope, and cut-off.
- [ ] Every enumerated status renders as a badge, with no plain-text statuses.
- [ ] Status badges use tiers consistent with the semantic mapping.
- [ ] Ratios carry a symbol or text anchor in addition to colour.
- [ ] `<colgroup>` declared at ≥8 columns; `table-layout:fixed` at ≥12.

**Fail examples:** a table cell showing `¥4.88 亿`; a total row that sums to `170%`; a null rendered as blank.

---

## C. Interaction

Perform each action in the browser.

- [ ] Changing any filter resets the page number to 1.
- [ ] "Clear filters" resets controls, chips, popover checkboxes, page number, and URL parameters.
- [ ] Cascade: changing an upstream select recomputes downstream options and clears downstream selections.
- [ ] Construct a zero-result filter → a specific empty message appears and the filters remain applied.
- [ ] Click a header → the arrow flips and the **full** result set reorders (verify on page 2).
- [ ] Navigate to the last page, then add a filter → the page clamps, no blank table.
- [ ] Click a drillable KPI → URL carries parameters, the detail page shows a back bar, conditions are inherited.
- [ ] Change filters on the detail page, then return → the main dashboard's filters are untouched.
- [ ] Hover a chart series → other series dim, tooltip follows the cursor and stays inside the viewport.
- [ ] Click export → filename matches `{scope}_{report}_{YYYYMMDD}.xlsx`, and the row count equals the **full filtered set**.
- [ ] Refresh the page → filters, sorting, and pagination are preserved.
- [ ] Trigger a slow load → a skeleton or spinner appears rather than a frozen page.
- [ ] Simulate an API failure → an error state with a retry action appears.

**Fail examples:** page stays at 9 after filtering to 3 pages; export contains only the visible 20 rows.

---

## D. Accessibility

Keyboard and screen reader.

- [ ] Using only `Tab`, `Shift+Tab`, `Enter`, `Space`, and `Esc`, complete: filter → sort → paginate → drill down → return.
- [ ] Every focusable element shows a visible focus ring.
- [ ] No element has `outline:none` without a replacement focus style.
- [ ] Sortable headers update `aria-sort` on click.
- [ ] Chart containers have `role="img"` + `aria-label`, or a hidden data-table alternative exists.
- [ ] An `aria-live` region announces the result count when filters change.
- [ ] Icon-only buttons carry `aria-label`; decorative elements carry `aria-hidden`.
- [ ] Tables have `scope="col"` on headers and a `<caption>`.
- [ ] Contrast for body text ≥ `4.5:1`; large text and UI boundaries ≥ `3:1`.
- [ ] Setting "reduce motion" in the OS disables transitions.
- [ ] Icon hit areas are ≥ `24×24px` (`44×44px` on touch).

**Fail examples:** a `div` with `onclick` and no `tabindex`; an SVG chart with no accessible name.

---

## E. Engineering and Robustness

- [ ] Open the file with the network disabled → styles and behaviour fully intact (no external dependencies).
- [ ] Swapping in a real API touches exactly one place in the code.
- [ ] An API timeout produces an error state with a retry action — not a white screen.
- [ ] A single failed column renders `—` with an `ⓘ` note while the rest of the report works.
- [ ] Browser console shows zero errors and zero warnings.
- [ ] No `innerHTML +=` anywhere in the rendering path.
- [ ] All user-controllable strings pass through an escape function.
- [ ] Page logic is wrapped in an IIFE or a module script.
- [ ] `<meta name="color-scheme" content="light">` is present.
- [ ] Wide tables use `position:sticky` for the header and first column.

**Fail examples:** a chart library loaded from a CDN that fails offline; `innerHTML +=` re-binding click handlers on every render.

---

## Reporting the Audit

For each failed item, record:

1. **Where** — file and selector or line.
2. **What** — the observed value versus the required value.
3. **Evidence** — screenshot, computed style, or console output.
4. **Severity** — blocking (breaks a must-rule) versus advisory (a should-rule).

Do not report a rule as satisfied without evidence. The purpose of this checklist is to convert a subjective design review into a set of verifiable claims.
