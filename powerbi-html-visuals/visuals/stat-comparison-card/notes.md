# Stat Comparison Card (Average vs Median / Skew)

**Use when:** the user wants a card comparing average vs. median for one or two
related numeric fields, calling out how much outliers are skewing the average
away from the "typical" value — plus a written insight line.

**Depends on:** HTML Content by Daniel Marsh-Patrick.

## Required inputs

| Token | What it must be |
|---|---|
| Metric 1 / Metric 2 columns | Two numeric fields to compare (can be just one — see note below) |
| Format expressions per metric | Each metric may need different scale/formatting (the source example uses plain € for rent, but €/M and €/K for the much larger sale price) — ask what units and rounding the user wants per metric |
| Title, metric labels, insight sentence 1 | Static text |

## Styling conventions specific to this visual

- Fixed card size 380×480px, taller than the KPI cards since it stacks two
  full metric sections plus an insight box.
- Skew tier pill (`OverallSkew`/`PillBg`/`PillColor`) uses thresholds of >40%
  ("High Skew"), >20% ("Moderate"), else "Low Skew" — taken across BOTH
  metrics' skew (`||` — if either exceeds the threshold, the whole card's pill
  reflects it). Confirm with the user whether that's the right combining rule
  if they want the two metrics' skew judged independently instead.
- The median-position tick mark (`.tm`) is positioned via `left:<median%-of-avg>%`
  on top of a fixed track — this visually shows "the median sits at X% of the
  way to the average," which only makes sense to interpret when median < average
  (positive skew). If the user's data can have median > average (negative
  skew), this tick can land past 100% or the visual metaphor may need rethinking
  — ask before assuming this handles negative skew correctly.

## ⚠️ Flagged inconsistency in the source file — needs verification before reuse

The original DAX (`Avg Median Skew HTML`) uses **percent-encoded characters
(`%23` for `#`, `%25` for `%`) inside a plain HTML string**, not inside a
`data:image/svg+xml` URI. Every other full-HTML card in this skill (KPI cards,
donuts, bar charts) writes literal `#1e2d3d` and literal `%` directly in their
`<style>` blocks — percent-encoding is only otherwise used in this repo for
the `CF *` badges and `_HTML_RevenueTarget`'s hint SVG, which genuinely are (or
contain) data URIs that need it.

This template preserves that percent-encoding **exactly as found in the
source**, unchanged, rather than silently "fixing" it — because it's possible
the HTML Content visual by Daniel Marsh-Patrick does its own decoding pass
that makes this work as authored (unconfirmed). **Before using this template
for a real report, test it once as-is; if colors/percentages render literally
as the text `%23089BAB` / `100%25` instead of being applied, strip the
percent-encoding back to plain `#` and `%` throughout and let us know so this
notes.md and the template can be corrected.**

## Known pitfalls / edge cases

- **Single-metric use**: if the user only wants one metric's avg/median
  comparison rather than two, the `<hr class='dv'>` divider and second `.sec`
  block can simply be omitted — reduce the card height accordingly (e.g.
  ~260px instead of 480px) rather than leaving empty space.
- `DIVIDE(AvgMetric - MedMetric, MedMetric)` breaks down if `MedMetric = 0` —
  no explicit third `DIVIDE` argument is supplied in the source, so this
  returns `BLANK()` silently; decide with the user if a fallback message is
  needed for that case.
