# KPI Card — Revenue vs Target (Flip Effect)

**Use when:** identical data/use-case to `kpi-card-target-slide` — same target,
attainment %, and gap — but the user specifically wants a 3D flip transition
instead of a horizontal slide. Purely an interaction-style choice; the required
inputs and DAX shape are the same as the slide variant.

**Depends on:** HTML Content by Daniel Marsh-Patrick. One measure, single value.

**Related variants:** `kpi-card-target-slide` (same data, slide transition
instead of flip), `kpi-card-simple-blink` (no target/gap — just value + prior
period variance, different data shape).

## Required inputs

Identical to `kpi-card-target-slide` — see that notes.md for the full input
table (`_rev`, `_target`, `_attain`, `_gap`, plus the label placeholders). Don't
duplicate that table here when adapting; just reuse the same four numeric
measures for whichever card style the user picks.

## Styling conventions specific to this visual

- Fixed card size: 420×280px (slightly different aspect ratio than the slide
  variant's 460×260 — match the HTML Content visual frame accordingly).
- Uses CSS 3D transforms (`perspective`, `transform-style:preserve-3d`,
  `backface-visibility:hidden`) rather than the slide variant's 2D translateX —
  this is a heavier render than the slide/blink variants. If a report has many
  of these cards on one page and performance is a concern, prefer slide or
  blink instead.
- Same tier-coloring convention and thresholds as the slide variant
  (`shared/color-palette.md`).

## Known pitfalls / edge cases

- All the same pitfalls as `kpi-card-target-slide` apply (hover doesn't work on
  mobile/PDF export, `_target` divide-by-zero guard, attainment >150% capping).
- **3D flip can look subtly wrong in some older Chromium-based render
  environments** if `perspective` isn't respected — if the user reports the
  flip looking flat/skewed rather than 3D, that's the first thing to check
  (some embedded/legacy Power BI Report Server renderers lag behind current
  Chromium; unconfirmed whether this specific repo's target environment is
  affected — ask if the flip doesn't render as expected).
- Don't mix this template's `.kc`/`.front`/`.back` class names with the slide
  variant's on the same report page/custom visual instance — they use the same
  class names with different CSS behavior, so if both are ever combined into
  one measure (they shouldn't be) the styles would collide.
