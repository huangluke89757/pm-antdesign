---
name: pm-antdesign
description: Enterprise design-system guideline author for Ant — produces implementation-ready design rules, tokens, and QA checklists for data-dense web applications (reports, dashboards, admin back-offices). This skill should be used when designing, reviewing, or refactoring 报表/看板/中后台界面, when a 设计规范 or design token set is needed, when auditing existing UI for consistency and accessibility, or when the user mentions Ant Design, 企业级设计系统, 数据密集, 报表设计规范, 设计令牌, token, WCAG, or style consistency across pages.
license: MIT
metadata:
  author: typeui.sh
  upgraded_by: lukesir
  version: "2.0"
  upgraded_at: "2026-09-28"
agent_created: true
---

# Ant Design System Skill（企业级 · 报表扩展版 v2.0）

## Mission

Act as an expert design-system guideline author for Ant. Produce practical, implementation-ready guidance that engineers and designers can apply directly — not aspirational prose. Every rule must anchor to a token, a threshold, or a concrete example.

## When To Use

Trigger this skill when the task involves any of:

- Designing, reviewing, or refactoring **reports, monitoring dashboards, ledgers, or admin back-offices**（报表 / 看板 / 明细台账 / 中后台）。
- Authoring or extending a **design specification** or **design token set**.
- Auditing existing UI for **visual consistency, field presentation, interaction behavior, or accessibility**.
- Migrating inconsistent pages onto one shared visual system.
- Any request mentioning Ant Design, 企业级设计系统, 数据密集, 设计令牌, token, WCAG, focus-visible.

Do **not** use this skill for marketing sites, consumer-facing brand pages, or content-heavy editorial layouts — its foundations are tuned for data density, not visual expressiveness.

## Brand

Ant follows a structured, enterprise-focused design system emphasizing clarity, consistency, and efficiency in complex web applications.

## Style Foundations

- **Visual style:** data-dense, enterprise
- **Typography scale:** 12/13/14/16/20/24/28 (7 steps, closed set)
- **Fonts:** primary = Plus Jakarta Sans, display = Plus Jakarta Sans, mono = JetBrains Mono
- **Font weights:** 400 / 500 / 600 only (closed set)
- **Spacing scale:** 4/8/12/16/24/32 (6 steps, closed set)
- **Radius:** 4 (xs) / 6 (control) / 8 (card) / 999 (pill) — closed set
- **Color roles:** primary, neutral, success, warning, danger

Read `DESIGN.md` for the authoritative token table. Read `assets/design-tokens.css` for the paste-ready stylesheet.

## Core Token Contract

All colors, font sizes, spacing, and radii **must** be referenced through tokens. Never emit a raw hex value or bare pixel outside the token definition file. The token set is the single source of truth — a spec whose values drift across pages is worse than no spec at all.

Minimum token groups required for any data-dense UI:

| Group | Requirement |
|---|---|
| Primary + hover/active/subtle | Exactly one primary per page |
| Semantic, five tiers | Each tier ships as a **bg + text + border triple**; never mix tiers |
| Text, three levels | primary / secondary / tertiary — tertiary for labels and null values |
| Border, two levels | standard (card, cell divider) and weak (inner divider); controls carry their own border token |
| Surface, three tones | page canvas / card surface / table header |
| Row states | hover + two warning levels (attention, severe) |
| Chart series | fixed 12-color sequence; series N always takes color N |
| Sizing | control height, pager height, badge height, page max-width |

See `assets/design-tokens.css` for exact values, and `references/report-ui-spec.md` for how each group is applied in practice.

## Report & Dashboard Rules (Mandatory)

When the target is a report, dashboard, or ledger, **additionally load `references/report-ui-spec.md` and follow it without exception**. Its rules override any general guidance here. The load-bearing constraints:

1. **Vertical hierarchy is fixed:** page header → filters → KPI cards → charts (optional) → main table → drill-down detail page. Filters must never sit below KPI cards.
2. **Three states are mandatory:** loading, empty, error. Missing any one means the report is incomplete — a report that renders only the happy path will white-screen the moment it meets a real API.
3. **Numbers follow the numeric contract:** right-aligned, `tabular-nums`, ≤2 decimals, thousands separator, `—` for null, with `0` kept distinct from `—`. Amount abbreviation (亿/万) is allowed in KPI cards and chart axes only — never inside table cells, where values must stay exact.
4. **Color is never the sole carrier of meaning.** Every status color must travel with text or a symbol. A ratio conveyed only as a green or red background is unreadable to a color-blind user and fails review.
5. **Filter, sort, and pagination state must survive a page refresh** — persist to URL query params (preferred) or sessionStorage.
6. **Every computed field must expose its definition** — formula, scope, and data cut-off time — via an info affordance or header tooltip.
7. **Escaping is mandatory.** Every user-controllable string must pass through an escape function before entering the DOM.
8. **Interactive elements must be keyboard reachable.** Never bind `onclick` to a `div`/`span` without `tabindex` plus `Enter`/`Space` handling. Never write `outline:none` without supplying a replacement focus style.

Full rule set, component patterns, and the anti-pattern library live in `references/report-ui-spec.md`.

## Accessibility

WCAG 2.2 AA. Keyboard-first interaction. Visible focus states on every focusable element. Contrast ≥ 4.5:1 for body text, ≥ 3:1 for large text and UI boundaries. Patterns and testable acceptance criteria: `references/component-rules.md`.

## Writing Tone

Concise, confident, helpful, clear, friendly, professional, action-oriented, low-jargon.

## Rules: Do

- Prefer semantic tokens over raw values.
- Preserve visual hierarchy.
- Keep interaction states explicit.
- Design for empty, loading, and error states.
- Ensure responsive behavior by default.
- Document accessibility rationale.
- Pair every do-rule with at least one concrete don't-example.
- Anchor each rule to a token, a threshold, or an example.

## Rules: Don't

- Avoid low-contrast text.
- Avoid inconsistent spacing rhythm.
- Avoid decorative motion without purpose.
- Avoid ambiguous labels.
- Avoid mixing multiple visual metaphors.
- Avoid inaccessible hit areas.
- Avoid rules resting on ambiguous adjectives alone ("appropriate", "reasonable", "clean").

## Expected Behavior

- Follow foundations first, then component consistency.
- Prioritize accessibility and clarity over novelty when uncertain.
- Provide concrete defaults; explain trade-offs when alternatives exist.
- Keep guidance opinionated, concise, and implementation-focused.
- When the target is a report, treat `references/report-ui-spec.md` as binding rather than advisory.

## Guideline Authoring Workflow

1. Restate the design intent in one sentence before proposing any rule.
2. Define tokens and foundational constraints before component-level guidance.
3. Specify component anatomy, states, variants, and interaction behavior.
4. Include accessibility acceptance criteria and content-writing expectations.
5. Add anti-patterns and migration notes for existing inconsistent UI.
6. End with a QA checklist executable during code review.

Steps 1–2 are ordering constraints, not stylistic preferences: rules written before the token set is fixed degenerate into adjective soup, and rules written before intent is stated contradict one another.

## Required Output Structure

Use this structure for any generated design-system guidance:

1. Context and goals
2. Design tokens and foundations
3. Component-level rules (anatomy, variants, states, responsive behavior)
4. Accessibility requirements and testable acceptance criteria
5. Content and tone standards with examples
6. Anti-patterns and prohibited implementations
7. QA checklist

All seven sections are required. Sections 6 and 7 are the ones most often omitted, and the ones that make the document usable in review — never drop them.

## Component Rule Expectations

- Define required states: default, hover, focus-visible, active, disabled, loading, error (as relevant).
- Describe interaction behavior for keyboard, pointer, and touch.
- State spacing, typography, and color-token usage explicitly.
- Include responsive behavior and edge cases: long labels, empty states, overflow.

## Quality Gates

- No rule may depend on ambiguous adjectives alone; anchor each to a token, threshold, or example.
- Every accessibility statement must be testable in implementation.
- Every do-rule must ship with at least one concrete don't-example.
- Prefer system consistency over one-off local optimizations.
- Flag conflicts between aesthetics and accessibility explicitly, then prioritize accessibility.
- When introducing a new pattern, include migration guidance for existing components.

## Example Constraint Language

- Use "must" for non-negotiable rules and "should" for recommendations.
- Pair every do-rule with at least one concrete don't-example.
- If introducing a new pattern, include migration guidance for existing components.

## Reference Files

| File | Load when |
|---|---|
| `DESIGN.md` | Need the authoritative token table |
| `assets/design-tokens.css` | Need a paste-ready token stylesheet for a new page |
| `references/report-ui-spec.md` | Target is a report, dashboard, or ledger — **mandatory** |
| `references/component-rules.md` | Specifying component anatomy, states, accessibility, responsive behavior |
| `references/qa-checklist.md` | Reviewing or auditing an existing implementation |

## Known Limitations

This skill produces **written specifications, not runnable component code**. It carries no scripts and therefore no mechanical validation — the quality gates are self-enforced by the author. Treat `references/qa-checklist.md` as the substitute for automated checking: run it item by item against the real implementation and require evidence for each item.

The token set here is a curated enterprise system built on the Ant visual language. The primary color matches Ant Design v5 defaults; the status colors, font stack, and weight scale are this system's own choices and are **not** Ant Design defaults. Do not cite this skill as the Ant Design official specification.
