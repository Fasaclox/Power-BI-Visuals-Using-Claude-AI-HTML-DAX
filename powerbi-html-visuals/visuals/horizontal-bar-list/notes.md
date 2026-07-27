# Horizontal Bar List (Ranked, Single Series)

**Use when:** the user wants a simple ranked list of categories each shown as a
horizontal track bar with a % and raw count — e.g. "distribution of X by
category," "breakdown of risk levels." One series only (for a 2-series
grouped comparison, use `clustered-bar` instead).

**Depends on:** HTML Content by Daniel Marsh-Patrick.

**Consolidates two near-identical originals from the source repo:**
`Sport Distribution Visual` (with per-row emoji icons, gradient fills) and
`Injury Risk Distribution Visual` (no icons, flat fills, wrapped in a shaded
row background). Both are the same underlying structure — this template keeps
the icon slot optional and documents both fill styles below rather than
maintaining two separate folders, since the difference is presentational
(icon vs. no icon, gradient vs. flat, wrapped row vs. bare) not structural.

## Required inputs

| Token | What it must be |
|---|---|
| `_Total` | Overall denominator for percentages |
| `_Val1`…`_ValN` | One measure per category, filtered | 
| `_Pct1`…`_PctN` | Computed from Val/Total — don't ask the user for these directly, derive them |
| Title | Static header text |
| Per-row icon (optional) | Emoji or short glyph — ask if the user wants icons at all; several existing rows use sport emoji (⚽🏃🏀🥊🎾) which is domain-specific and shouldn't be reused outside a sports context |
| Fill color per row | Either a flat hex or a `linear-gradient(...)` — see styling notes below |

## Styling conventions specific to this visual (two fill sub-styles seen in source)

- **Gradient style** (`Sport Distribution Visual`): each row's fill is a
  `linear-gradient(90deg, colorA, colorB)` where the ramp gets progressively
  lighter down the list (`#1a2b4a→#244181` for row 1, lightening toward
  `#5a8fe0→#7aa8eb` for the last row) — implies a size-ordered list (largest
  category first, darkest color first).
- **Flat style with wrapped rows** (`Injury Risk Distribution Visual`): each
  row sits in its own `#f3f5f7` background box (`.rd-row` wrapper, not present
  in this generalized template's `.sd-row` — add a wrapping background div per
  row if this look is wanted), and fill colors map to a fixed severity meaning
  (green/amber/orange/red) rather than a size-based gradient ramp.
- Ask the user which of these two looks they want — size-ranked gradient
  (works well when order = magnitude), or severity-flat-colored with row
  backgrounds (works well when categories have inherent risk/status meaning
  regardless of size).

## Known pitfalls / edge cases

- **Fixed row count, hand-written per category** — same limitation as
  `stacked-bar-100`: this is not dynamically generated from a table, each row
  is its own VAR block. Not suitable for a dimension with many/unpredictable
  members without restructuring.
- If categories aren't naturally ordered by size and the user picks the
  gradient style, the color ramp will look arbitrary rather than meaningful —
  confirm sort order intent before choosing gradient vs. flat-severity colors.
