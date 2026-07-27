# CF Icon Status Row (Multi-Amenity / Multi-Flag Badge)

**Use when:** a table/matrix row needs several yes/no columns condensed into one
compact row of colored icon squares (e.g. amenities: parking, gym, pool,
elevator), rather than separate columns or separate `cf-status-badge` pills.

**Depends on:** nothing custom — native Power BI Image URL data category, same
mechanism as `cf-status-badge`. See `shared/conditional-format-binding.md`.

## Required inputs

| Token | What it must be |
|---|---|
| `_flag1`…`_flag4` | One boolean expression per icon (e.g. `[gym] = "Yes"`, `[parking_spots] > 0`) — this template ships with 4 slots; add/remove `VAR` blocks and adjust the SVG `width`/gap math for a different count |
| Icon paths (`_m1`…`_m4`) | Default is a generic check (true) / X (false) glyph shared by all slots. If the user wants recognizable per-amenity icons (car, dumbbell, pool wave, elevator), each needs its own true/false path pair — ask which flags need custom icons vs. generic check/cross |

## Styling conventions specific to this visual

- On-color: `#089BAB` (teal, matches the real-estate CF badge family in
  `shared/color-palette.md`) / off-color: `#cccccc`. Swap both together if a
  different palette applies — don't recolor only the "on" state.
- Fixed overall SVG size (`92×22` for 4 icons at `18px` boxes with `22px` gap
  spacing) — **this width must be recalculated by hand if the icon count
  changes**: total width ≈ `1 + (n × gap)` where `gap=22`, box size `18`. There
  is no dynamic-width formula in the source; if the user needs 5+ icons
  regularly, it's worth building that as a proper `n`-driven width expression
  rather than repeating this by-hand math each time.

## Known pitfalls / edge cases

- **This does not scale to a variable number of flags per row** — the number of
  icon slots is fixed at DAX-authoring time, not driven by a dynamic list. If
  the user wants "however many amenities apply, however many that is,"
  that's a different (more complex) pattern than this template — flag that
  distinction if asked for something that sounds dynamic-length.
- Same sorting/tooltip pitfalls as `cf-status-badge` apply (no automatic
  tooltip content, sort is by the raw string not any logical order).
- **Adding a 5th+ icon requires updating the SVG canvas `width` attribute** as
  well as adding the new rect/path pair — a common mistake is adding the icon
  block but forgetting to widen the outer `<svg>`, which clips the last icon.
