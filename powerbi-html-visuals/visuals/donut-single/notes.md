# Donut Single (Multi-Segment)

**Use when:** the user wants one donut chart breaking a total into 3+ mutually
exclusive segments with a legend, and the total shown in the center.

**Depends on:** HTML Content by Daniel Marsh-Patrick.

**Related variant:** `donut-grid` — small-multiple donuts, one per category,
each showing just that category's share of its own total (not one shared donut
with 3+ segments). Use `donut-single` when there's ONE total being split into
segments; use `donut-grid` when there are SEVERAL independent categories each
needing their own share-of-whole shown side by side.

## Required inputs

| Token | What it must be |
|---|---|
| `_Total` | Overall denominator |
| `_SegA`, `_SegB`, `_SegC`, ... | One measure per segment — must be mutually exclusive and sum to `_Total` (or close to it) |
| Segment colors | One per segment, used for both the arc stroke and legend dot |
| Segment labels | Legend text |
| Title, center label | Static text (center label is usually just "Total") |

## Styling conventions specific to this visual

- Built from **stacked SVG `<circle>` strokes** using `stroke-dasharray` +
  `stroke-dashoffset`, rotated -90° so the first segment starts at 12 o'clock.
  This is the trickiest math in the whole skill to get right when adding
  segments — **each new segment's `stroke-dashoffset` must be the negative
  running total of every arc-length before it**, not just its own arc length.
  Getting this wrong causes segments to overlap or leave gaps. Double-check the
  running-sum math explicitly rather than pattern-matching from the last
  segment when adding a 4th, 5th, etc.
- `_Circ = 251.33` is the circumference of the specific `r=40` circle used here
  (`2 × π × 40`) — if the donut's radius changes for a different visual size,
  recompute `_Circ` to match, don't leave it hardcoded to the old radius.
- Center displays the **total**, not a computed metric like an average — if the
  user wants something else in the center (e.g. the largest segment's %,) that's
  a straightforward swap but confirm which they want.

## Known pitfalls / edge cases

- **Segments must be mutually exclusive.** If the user's categories can overlap
  (e.g. multi-select tags rather than a single status), percentages won't sum
  to 100% and the arc math will look wrong — confirm the segments are a true
  partition of the total before using this pattern.
- If segment percentages don't sum to exactly 100 (rounding), the very last
  segment's arc may visually undershoot/overshoot slightly — this is normal at
  the 0.1% level from `ROUND(...,1)` but call it out if the user is displaying
  the percentages as text elsewhere and expects them to reconcile exactly.
