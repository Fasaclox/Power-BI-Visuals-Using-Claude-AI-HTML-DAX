# KPI Card — Revenue vs Target (Slide Effect)

**Use when:** a single KPI needs to be shown against a target, with attainment %
and remaining gap, and a hover-reveal detail panel is acceptable (desktop-viewed
reports only — see pitfall below).

**Depends on:** HTML Content by Daniel Marsh-Patrick. One measure, single value.

**Related variants:** `kpi-card-target-flip` (same data shape, 3D flip instead of
slide), `kpi-card-simple-blink` (no target/gap — just value + prior-period
variance). Ask the user which interaction they want if not specified; default to
this one (slide) unless they ask for flip/blink by name.

## Required inputs

| Token in template | What it must be | Format |
|---|---|---|
| `_rev` | Current period total (e.g. total revenue) | Raw number, NOT pre-formatted as currency |
| `_target` | Target value for the same period/filter context | Raw number |
| `_attain` | `_rev / _target` | Decimal, e.g. `0.82` — do not pass as already-percent |
| `_gap` | `_target - _rev` | Raw number, can be negative (over-target) |
| Label: metric name | e.g. "Gross Revenue" | Static text or a measure, your choice |
| Label: period description | e.g. "Target Period — FY 2025" | Static text or dynamic via `SELECTEDVALUE` |
| Label: back-face tag/title | Short header text for the hover-reveal face | Static text |

All four numeric VARs should be their own measures or expressions computed
**before** this measure, so this measure is purely presentation. If the user's
model already has `[TotalRevenue]`, `[RevenueTarget]`, etc. as existing measures,
reference those directly rather than recomputing `_attain`/`_gap` inline.

## Styling conventions specific to this visual

- Fixed card size: 460×260px — match the HTML Content visual's frame to this,
  don't stretch it.
- Tier coloring (`t-green`/`t-blue`/`t-orange`/`t-red`) follows the traffic-light
  convention in `shared/color-palette.md` — reuse those exact thresholds
  (≥100%, ≥70%, ≥40%, below) unless the user specifies different attainment bands.
- The checkmark icon (`&#10004;`) only appears once `_attain >= 1`; otherwise a
  ring character (`&#9678;`) is shown. Don't swap this for an emoji — the HTML
  entity renders consistently across Power BI's Chromium-based render surface,
  emoji font support has been flaky in some Report Server deployments (unconfirmed
  for this repo specifically — flag if the user reports icon rendering issues).

## Known pitfalls / edge cases

- **Hover doesn't work on mobile/touch or in exported PDF/PPT.** If this report
  needs to work there, this card is the wrong choice — ask about `kpi-card-simple-blink`'s
  static equivalent or suggest showing both faces stacked instead of hover-toggled.
- **`_attain` above ~150%** will visually cap the bar fill at 100% width (via
  `MAX(MIN(_attain*100,100),6)`) but the pill and gap text will still show the true
  number — this is intentional (bar caps, numbers don't) but confirm with the user
  it's the behavior they want for very-over-target scenarios.
- **Negative `_target`** or `_target = 0` will break `_attain` (divide-by-zero or
  nonsensical percentage) — add a guard (`DIVIDE` instead of `/`, or an `IF` branch)
  if the user's target measure can ever be blank/zero.
