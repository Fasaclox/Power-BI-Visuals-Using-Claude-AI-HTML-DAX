# Stacked Bar 100% (Horizontal, Fixed Categories)

**Use when:** the user wants to compare how a fixed set of categories (e.g.
sports, product lines, regions) breaks down across the SAME set of 3-5 segments
(e.g. risk tiers, satisfaction levels) — each category's bar always sums to 100%.

**Depends on:** HTML Content by Daniel Marsh-Patrick.

## Required inputs

| Token | What it must be |
|---|---|
| Category list | A fixed, known-in-advance list (this pattern is **hardcoded per category in DAX**, not dynamically generated from a table — see pitfall) |
| Per-category total measure | Used as the denominator for each segment's % |
| Per-category-per-segment measures | 3-5 segment values per category, each divided by that category's total |
| Segment labels + colors | Legend text and the 3-5 fixed segment colors |
| Title | Static header text |

## Styling conventions specific to this visual

- Segment colors default to a 4-tier severity ramp: red `#D93025` → amber
  `#F59E0B` → blue `#3B82F6` → green `#22C55E`. This reads as "worst to best" —
  keep segment order consistent with that reading unless the user's segments
  aren't a severity scale, in which case ask what color order makes sense
  (e.g. neutral categorical colors instead of a red-to-green ramp).
- Bar segments use `flex:<percent>` rather than `width:<percent>%` — this is
  deliberate so rounding errors that don't sum to exactly 100 still fill the
  bar completely (flex-basis proportional fill), rather than leaving a visible
  gap. Don't convert this to fixed `width` percentages.

## Known pitfalls / edge cases

- **This is NOT dynamically generated from a category dimension** — every
  category is its own hand-written block of `CALCULATE` VARs in the source.
  Adding a new category means writing a new block, not adding a row to a table.
  If the user has more than ~6-8 categories or an unpredictable/changing list,
  this is the wrong pattern — recommend `horizontal-bar-list` (single-measure,
  still fixed-category style) or flag that a fully dynamic version would need
  restructuring around `SUMMARIZE`/`CONCATENATEX` instead (not built yet — say
  so rather than guessing at one).
- **Rounding**: each segment is independently rounded to the nearest whole
  percent, so a category's segments can sum to 99% or 101% rather than exactly
  100 — this is normal for this pattern and usually invisible with `flex`
  fill, but mention it if the user is showing the raw numbers anywhere else
  and expects them to reconcile exactly.
- If a category's total is 0 (`DIVIDE` denominator), guard with `DIVIDE(...,
  ..., 0)` as the third argument (already done in the source) so it returns 0
  instead of erroring — don't drop that third `DIVIDE` argument when adapting.
