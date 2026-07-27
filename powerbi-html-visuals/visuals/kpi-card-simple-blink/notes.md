# KPI Card — Simple Metric vs Prior Period (Blink Effect)

**Use when:** a single metric needs to be shown with a simple up/down variance
badge against the prior period — **no target/attainment/gap concept**. This is
a genuinely different data shape from the other two KPI cards, not just a
different hover style — don't substitute this in for a "progress to target"
request, and don't substitute the target cards in for a plain "how did this
change vs last month" request.

**Depends on:** HTML Content by Daniel Marsh-Patrick. One measure, single value.

**Related variants:** `kpi-card-target-slide`, `kpi-card-target-flip` — both
need a target measure this one doesn't use. If the user only has a "vs prior
period" comparison and no target, this is the right card; if they mention a
target/goal/quota, use one of the other two instead.

## Required inputs

| Token in template | What it must be | Format |
|---|---|---|
| `_rev` | Current period value | Raw number, not pre-formatted |
| `_revVar` | % change vs prior period | Decimal (e.g. `0.045` for +4.5%) — can be `BLANK()` if there's no valid prior period (handled via `ISBLANK`) |
| `_revDelta` | Absolute change vs prior period, same unit as `_rev` | Raw number, signed |
| `_selectedDate` | The current period's date, typically `MAX('Calendar'[Date])` in context | Date |
| Icon path (front + back) | An SVG `<path>`/shape matching the metric | Two versions needed — one with a dark stroke for the white front face, one with a light stroke for the dark back face |
| Label: metric name | e.g. "Total revenue" | Static or dynamic text |
| Label: back-face tag | Short header for the back face | Static text |

## Styling conventions specific to this visual

- This template wraps its HTML in an inline SVG `<foreignObject>` rather than
  rendering the `<div>` directly — that's intentional in the source and keeps
  the card scaling proportionally within the HTML Content visual's frame
  (`viewBox='0 0 460 260'` with `preserveAspectRatio='xMidYMid meet'`). Don't
  strip this wrapper when adapting; if the card looks distorted, check the
  frame's aspect ratio matches 460:260 rather than removing the `<svg>` layer.
- "Blink" here means an instant opacity swap (`transition:opacity 0.15s
  steps(1)`), not a smooth cross-fade — this is deliberately snappier than the
  slide/flip variants. If the user wants a smoother transition, point them to
  the slide or flip target-card variants instead (which have that data shape)
  or ask if they want this same data shape with an eased fade — that would be
  a one-line CSS change (`steps(1)` → `ease`) if requested.
- Icon color pairing: front icon uses a dark stroke on a light circle
  (`#5C3D2E` on `#F5EFE6`), back icon uses light stroke on a dark circle
  (`#F5EFE6` on `#7A5240`) — keep this contrast pairing when swapping icons so
  they stay visible on both faces.

## Known pitfalls / edge cases

- **`_revVar` blank handling**: if there's no prior period (e.g. first month of
  data), `_revVar` should be `BLANK()`, not `0` — the template already displays
  `"--"` for blank via `ISBLANK`, but a `0` would incorrectly render as "+0.00%"
  and imply no change rather than "no comparison available."
- **Hover doesn't work on mobile/touch or in exported PDF/PPT** — same
  limitation as the other two KPI cards.
- If the user wants this card for a metric that ISN'T month-over-month (e.g.
  week-over-week, quarter-over-quarter), the `EDATE(_selectedDate, -1)` and
  `"mmm-yyyy"` formatting need to change together — ask what period grain
  they're comparing before adapting.
