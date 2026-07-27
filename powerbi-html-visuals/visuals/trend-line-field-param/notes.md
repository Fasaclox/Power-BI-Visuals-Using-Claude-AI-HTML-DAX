# Trend Line (Field Parameter Switchable)

**Use when:** the user wants a line/area trend chart over a fixed set of
periods (months, quarters, weeks) where the metric being plotted is
switchable at report-view time via a Power BI **Field Parameter** slicer —
not multiple separate charts, one chart that redraws when the viewer picks a
different measure.

**Depends on:** HTML Content by Daniel Marsh-Patrick, PLUS a Field Parameter
already created in the model (Modeling ribbon → New Parameter → Fields) with
one entry per switchable measure.

## Required inputs — ask these before writing anything

1. **What are the switchable options**, and what measure does each one map to?
   (the source example: "Injuries" → `[Recorded Injuries]`, "Total Cost" →
   `[Total Cost]`, "Recovery Days" → `[Average Days To Recovery]`)
2. **What's the period grain and count** — 12 months? 4 quarters? 7 days? This
   determines how many `_P#`/`_X#`/`_Y#` blocks to write and what `_Step`
   divides by (`period count - 1`).
3. **Does each option need different number formatting?** (e.g. currency for
   cost, plain integer for a count, decimal for an average) — `_Fmt` is
   currently a single shared format string; if formats genuinely differ per
   option, `_Fmt` needs to become a `SWITCH(_Sel, ...)` itself.
4. **What color per option** — feeds `_Color`, which drives the line, dots,
   and area-fill gradient all at once.

## Styling conventions specific to this visual

- The chart is dynamically padded: `_Pad` adds 15% of the value range above
  and below the min/max so the line doesn't touch the very top/bottom of the
  plot area — don't remove this, a full-bleed line looks visually cramped and
  clips point labels.
- Point value labels float above each dot (`_Y# - 10`) — same headroom
  requirement as `clustered-column`'s bar labels; the padding above accounts
  for this already via `_Pad`.
- The area fill under the line uses a `linearGradient` fading from 25% to 2%
  opacity of `_Color` — this ties the fill color to whichever option is
  selected automatically, since `_Color` is itself a `SWITCH` on `_Sel`.

## Known pitfalls / edge cases

- **This is the most math-heavy template in the skill** — X/Y coordinates for
  every period follow a fixed formula (`Y = H - ((value-_Bot)/_Range)*H + 20`,
  `X = 30 + _Step * index`). When adding/removing periods, recompute `_Step`
  (`_W / (period_count - 1)`) and make sure every `_P#`, `_X#`, `_Y#` triple
  is added consistently — a mismatched count between the CALCULATE blocks and
  the coordinate blocks is the most likely bug when adapting this.
- **The polygon (area fill) point list must end by closing back down to the
  baseline** — the last two points in the `polygon` must be `(last_X,
  _Base)` and `(first_X, _Base)` to close the fill shape at the bottom; the
  `polyline` (the line itself) does NOT include those closing points. Don't
  copy the polygon's point list verbatim into the polyline or the line will
  incorrectly drop back to the baseline at both ends.
- **Field Parameter must exist in the model first** — this template assumes
  the parameter table/column already exists; if the user hasn't set one up
  yet, that's a modeling step outside this measure (Modeling → New Parameter
  → Fields, selecting the candidate measures) — walk them through creating it
  before writing this measure, don't assume it's already there.
- If `_Sel` doesn't match any `SWITCH` branch (e.g. parameter renamed after
  this measure was written), `_Color` and every `_P#` will silently fall back
  to blank/0 — keep the `SWITCH`'s option names in exact sync with the actual
  Field Parameter's display values.
