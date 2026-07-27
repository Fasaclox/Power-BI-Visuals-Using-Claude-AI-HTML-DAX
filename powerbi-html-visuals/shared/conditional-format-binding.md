# Conditional-Format Badge Binding — General Guide

Covers the `CF *`-style visuals: single-column, per-row badges rendered as inline
SVG via a `data:image/svg+xml` URI. These are structurally different from the
full-canvas HTML Content cards (see `shared/html-visual-binding.md`) — no custom
visual is required.

## How this binds in Power BI (no custom visual needed)

1. Write a DAX measure (or calculated column) that returns a
   `data:image/svg+xml;utf8,<svg>...</svg>` string, built per-row from the column
   being conditionally formatted.
2. Add that measure/column to a **Table** or **Matrix** visual.
3. Select the field in the table → **Column headers / Values formatting** →
   set the field's **Data category** to `Image URL` (Modeling ribbon → Data Category
   → Image URL).
4. Set the table's cell display to render as an image (in the table's Format pane,
   under the column's settings, enable "Image size" and pick a fit — usually
   "Normalize" or "Fit" at a small fixed height matches the SVG's own `height`).

Because this renders through Power BI's native image-URL handling rather than a
custom visual, it works in more contexts (mobile, some export paths) than the
HTML Content cards do — but it's also more limited: no hover states, no JS, no
multi-row layout, one badge per cell only.

## Percent-encoding rules (important — these break silently, not loudly)

The whole SVG has to survive being a URI, not just being valid markup:

- `#` (used constantly in hex colors) → `%23`
- Any literal space in a `transform` or coordinate list → keep as-is (spaces are
  tolerated in most browsers/Chromium renderers used by Power BI, but if a badge
  fails to render, try `%20` first)
- Single quotes `'` for all SVG attribute delimiters — never double quotes, since
  the outer DAX string is already double-quote-delimited
- Do NOT URL-encode the SVG element tags themselves (`<svg>`, `<rect>`, `<text>`) —
  only encode characters that are reserved in URIs (mainly `#`)

## Sizing rules

- Badge `width`/`height` on the `<svg>` tag must match (or be smaller than) the
  table's configured image cell size, or Power BI will letterbox/crop it.
- For variable-length text badges (e.g. property IDs, "Semi-Furnished" vs "Rental"),
  compute width dynamically from string length in DAX (see `CF Property ID Col`
  pattern: `MAX(110, LEN(_id) * 7 + 30)`) rather than hardcoding — a hardcoded width
  will clip longer values in other locales/datasets.

## Known pitfalls

- **Table sort/filter by this field sorts by the raw string measure, not by any
  visual tier** — if you want "Fast/Moderate/Slow/Stale" to sort in that logical
  order rather than alphabetically, you need a separate hidden sort-by column/measure
  with the tier's rank, and set "Sort by column" on the display field to that.
- **Tooltips on image cells default to nothing useful** (no automatic label) —
  if a tooltip is needed, it has to be built as a separate report-page tooltip or
  a text column shown elsewhere in the same row.
- **Color values must be pre-picked per tier in DAX**, not passed through
  Power BI's own conditional formatting UI — this is a fully custom badge, not a
  layer on top of PBI's built-in background/font-color rules.
