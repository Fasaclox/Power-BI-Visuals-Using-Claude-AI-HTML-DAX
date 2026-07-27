# Category Card Grid

**Use when:** the user wants "a card per country/region/product/category" in a
grid, each showing a total plus a 2-way breakdown (e.g. gender, pass/fail,
yes/no) — sorted by size. Originally built for country + gender in the source
repo but the pattern generalizes to any single grouping dimension with a 2-way
split.

**Depends on:** HTML Content by Daniel Marsh-Patrick. One measure, built via
`CONCATENATEX` over the grouping dimension.

## Required inputs

| Token | What it must be |
|---|---|
| Grouping dimension | The column to make one card per member of (e.g. `DimLocation[Country]`) |
| `@Total` measure | Overall count/sum for that category |
| `@SplitA` / `@SplitB` measures | The same total filtered two ways (must be exactly 2-way — see pitfall below for 3+) |
| `@Code` (optional) | A lookup used to build an image URL per category (flag, logo) — omit the image block entirely if not needed |
| Title text | Static string for the grid header |
| Unit label | e.g. "players", "units" — what's being counted |
| Split A/B symbols | Single character or short glyph shown in each split's icon circle |

## Styling conventions specific to this visual

- 3-column CSS grid (`grid-template-columns:repeat(3,1fr)`) — fixed at 3 columns
  regardless of category count; ask the user if they want a different column
  count for very short or very long category lists (e.g. 2 columns for <6
  categories, or a scrolling single row).
- Cards sort descending by `@Total` via `CONCATENATEX`'s own ordering
  argument — change `[@Total], DESC` to alphabetical or another sort key if asked.
- The flag-image pattern in the source (`https://flagcdn.com/w80/{code}.png`)
  is specific to country flags — for any other image-per-category need (team
  logos, product photos), the URL pattern and the `@Code` lookup will need
  rebuilding entirely; don't assume flagcdn.com applies outside a
  country-grouped visual.

## Known pitfalls / edge cases

- **Only supports a 2-way split**, not 3+. If the user wants 3+ segments per
  card (e.g. age brackets instead of gender), this template's `.cc-row` layout
  (two `.cc-g` blocks + one divider) needs restructuring — don't just add a
  third `@SplitC` without redesigning the row layout, it'll break the
  side-by-side flex spacing.
- **No pagination** — see the general CONCATENATEX pitfall in
  `shared/html-visual-binding.md`. A grouping dimension with many members (50+)
  will produce a very large single HTML string and can slow down re-render on
  filter changes. Ask how many distinct categories are expected before using
  this for a high-cardinality dimension.
- If `@Code`/image lookup has no match for a given category (`SWITCH` falls
  through to a default), decide with the user what the fallback should be
  (blank image, generic placeholder icon, or omit the image block for that
  card only) rather than letting a broken image URL render.
