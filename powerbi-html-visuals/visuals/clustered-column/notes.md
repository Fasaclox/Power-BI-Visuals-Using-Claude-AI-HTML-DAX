# Clustered Column (2-Series, Vertical)

**Use when:** same use case as `clustered-bar` — comparing exactly 2 series
across several groups — but the user wants vertical columns instead of
horizontal bars (typically preferred when group names are short, since labels
sit below the columns rather than needing horizontal room).

**Depends on:** HTML Content by Daniel Marsh-Patrick.

**Related variants:** `clustered-bar` (horizontal orientation), `stacked-column`
(same 2 series but summed into one bar per group instead of side by side).

## Required inputs

Same shape as `clustered-bar` — group list (fixed, hand-written), Series A/B
measures per group, `_Max` computed globally across all group×series values,
labels/title/subtitle. See `clustered-bar/notes.md` for the full input table;
don't duplicate it here, the only difference is bar orientation and the fixed
`_Scale = 150` (px) height cap used to convert values to heights instead of widths.

## Styling conventions specific to this visual

- Column height is `DIVIDE(value, _Max, 0) * _Scale` where `_Scale = 150` (px)
  — this is the chart's max column height, not a percentage; if the visual's
  frame is much taller/shorter than the source's assumed proportions, adjust
  `_Scale` to fit rather than leaving columns too short or clipped.
- Value labels float **above** each column (`top:-16px`), not inside it like
  the bar variant — this only works if there's headroom above the tallest
  possible column; the `.gc-chart` container's `height:180px` needs to exceed
  `_Scale` by enough margin for the label, or the tallest bar's label will clip
  at the top of the visual.
- Group labels (icons + name) sit in a separate row below the bars
  (`.gc-labels`), aligned by matching `gap` values to `.gc-chart` — if the
  number of groups changes, both the `.gc-chart` and `.gc-labels` gap/width
  math need to stay in sync or labels will drift out from under their bars.

## Known pitfalls / edge cases

- Same fixed/hand-written group list limitation as the other chart visuals in
  this skill.
- Same non-negative-values assumption as `clustered-bar` — this pattern isn't
  built for series that can go negative.
- **Keep `.gc-chart` and `.gc-labels` group counts in lockstep** — a common
  mistake when adapting is adding a new `.gc-group` bar block but forgetting
  the matching `.gc-lbl` entry (or vice versa), which misaligns every label
  after the missing one.
