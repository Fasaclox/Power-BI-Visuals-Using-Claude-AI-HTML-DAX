# Clustered Bar (2-Series, Horizontal)

**Use when:** the user wants to compare exactly 2 series (e.g. gender,
before/after, pass/fail) across several groups (e.g. age buckets), with
horizontal bars scaled relative to the single largest value across the whole
chart — not one bar per group but two stacked-in-place bars per group.

**Depends on:** HTML Content by Daniel Marsh-Patrick.

**Related variant:** `clustered-column` is the same 2-series comparison but
vertical bars instead of horizontal — ask which orientation the user wants if
not specified. `stacked-column` is for when the two series should sum into one
bar per group instead of sitting side by side.

## Required inputs

| Token | What it must be |
|---|---|
| Group list | Fixed, hand-written per group (age bucket, category, etc.) — same "not dynamic" limitation as other chart visuals in this skill |
| Series A / Series B measures | Exactly 2 series, one per group |
| `_Max` | Computed as the max across ALL group×series values — this is what all bar widths scale against, not each group's own max |
| Group labels, series labels, title, optional subtitle | Static text |

## Styling conventions specific to this visual

- Bar width is relative to the **global max across every group and series**,
  not per-group — this means a group with small values will show visually
  short bars even if it's proportionally significant within its own group.
  That's intentional for comparing absolute magnitude across groups; if the
  user wants each group normalized to its own 100%, that's a different
  chart (closer to `horizontal-bar-list` repeated per group) — clarify intent.
- Series A (first listed) uses a darker navy gradient (`#0d1b30→#244181`),
  Series B a lighter blue gradient (`#3a6bbf→#7aa8eb`) — this pairing is
  domain-specific (was gender in the source). For a non-gender 2-series
  comparison, ask if a neutral color pair should replace this, per the
  guidance in `shared/color-palette.md`.
- Bar value labels are printed inside the bar, right-aligned
  (`justify-content:flex-end`) with a `min-width:30px` floor so short bars
  still show their number — don't remove the `min-width`, very small bars
  will otherwise clip the label.

## Known pitfalls / edge cases

- **Fixed, hand-written group list** — same limitation noted throughout this
  skill's chart visuals. Not suitable for a dynamic/unpredictable group count
  without restructuring around `SUMMARIZE`.
- If a series can be negative (e.g. a variance rather than a count), this
  template's `DIVIDE(..., _Max, 0)` width math breaks down — this pattern
  assumes non-negative magnitudes only. Flag to the user if their data can go
  negative; a different chart shape is needed for that.
