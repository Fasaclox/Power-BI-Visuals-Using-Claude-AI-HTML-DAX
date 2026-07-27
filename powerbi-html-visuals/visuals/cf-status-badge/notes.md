# CF Status Badge (Conditional-Format Pill)

**Use when:** a single categorical or tiered-numeric column in a table/matrix
needs a colored pill badge instead of plain text — e.g. status, category, or a
numeric field bucketed into tiers (fast/moderate/slow, A–G rating, etc.).

**Depends on:** nothing custom — native Power BI Image URL data category on a
table/matrix column. See `shared/conditional-format-binding.md` for the full
binding steps (data category + cell image sizing).

**Consolidates these originals from the source repo:** `CF DOM Col`, `CF DOM Col2`,
`CF Listing Col`, `CF Furnishing Col`, `CF Energy Col`, `CF Psqm Col`,
`CF Property ID Col` — all of these are the *same* template with different
`Label`/`TextColor`/`BorderColor`/`BgColor`/`BadgeWidth` logic. Rather than one
folder per column, this is documented as one generic template with the
per-column decisions written up below as worked examples, since the underlying
mechanism and pitfalls are identical.

## Required inputs

| Token | What it must be |
|---|---|
| `_val` | The raw column or measure being classified (categorical text, or a number to bucket) |
| `Label` | Either `_val` directly, or a `SWITCH(TRUE(), ...)` deriving a short display label |
| `TextColor` / `BorderColor` / `BgColor` | Percent-encoded hex per tier (`#` → `%23`) — pick from `shared/color-palette.md`'s teal/amber convention for this family, or ask the user if a different palette applies |
| `BadgeWidth` | Fixed string width (e.g. `"48"`) if categories are short/known-count, or the dynamic `LEN()`-based version if text length varies |

## Worked examples from the source repo (for reference when adapting)

- **Numeric → tiered label** (`CF DOM Col`, days-on-market): `_d < 30` → "Fast",
  `_d < 120` → "Moderate", `_d < 300` → "Slow", else "Stale". Each tier gets its
  own fixed `BadgeWidth` since label lengths differ ("Fast"=40px, "Moderate"=66px).
- **Numeric → same-format label, no bucketing** (`CF DOM Col2`): shows the raw
  number with a unit suffix (`FORMAT(_d,"#,##0") & "d"`) but still colors by the
  same tier thresholds as DOM Col — this is the "show the number but color it"
  variant vs. "replace the number with a tier word" variant. Ask the user which
  they want; don't assume.
- **Categorical, 2 options** (`CF Listing Col`): `Rental` vs `Sale`, fixed width
  per option since there are only two known strings.
  **Categorical, 3 options** (`CF Furnishing Col`): `Furnished` / `Semi-Furnished` /
  other, same pattern, three fixed widths.
- **Ordinal rating A–G** (`CF Energy Col`): 7 fixed tiers, each with its own
  color — a green→red ramp. Fixed small width (28px) since all labels are 1 char.
- **Numeric, currency-style with a value-based (not category-based) color ramp**
  (`CF Psqm Col`): note this one switches on `_p <` thresholds but does NOT use
  the teal/amber-family colors consistently with the others — it uses text color
  that flips from dark to white as background darkens. This is a legitimate
  different pattern (contrast-safe badge on a filled/darkening background) —
  keep this distinction in mind if asked for a "heat scale" badge rather than a
  "status tier" badge.
- **Dynamic width from string length** (`CF Property ID Col`): `MAX(110, LEN(_id)*7+30)`
  — use this approach whenever the label is a free-text/ID field rather than a
  small fixed set of categories.

## Known pitfalls / edge cases

- All the general pitfalls in `shared/conditional-format-binding.md` (sorting by
  raw string not tier rank, tooltips, data category setup) apply here.
- **Don't reuse the exact same `BadgeWidth` across differently-worded tiers** —
  e.g. "Fast" and "Moderate" need different fixed widths or text will clip/float
  off-center. Compute or hardcode per-label.
- **If the user asks for a NEW categorical field**, the questions to ask before
  writing the measure: (1) how many distinct categories/tiers, (2) do any exceed
  ~8 characters (decide fixed vs dynamic width), (3) is there an existing brand
  palette to match, or should it default to `shared/color-palette.md`.
