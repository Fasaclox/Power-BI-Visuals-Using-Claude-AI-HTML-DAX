# HTML Visual Binding — General Guide

This file covers what's common across every visual in `visuals/` that renders as a
full HTML/CSS card (KPI cards, donuts, bar/column charts, country cards, etc.).
It does NOT cover the `CF *` conditional-formatting badges — see
`shared/conditional-format-binding.md` for those (different mechanism, no custom visual needed).

## Custom visual dependency

All full-canvas HTML visuals in this skill depend on:

**HTML Content** by Daniel Marsh-Patrick (OKViz)

- Import it into the report via AppSource or a `.pbiviz` file before any of these
  templates will render.
- The visual takes exactly **one measure** in its "Values" / HTML field well. That
  measure must return a single text string containing the complete `<style>...</style>`
  plus markup for that visual — never split across multiple measures or fields.
- The visual re-evaluates the measure per the current filter context, same as any
  other measure — so all filtering, slicers, and cross-highlighting work as normal
  DAX context, not as separate visual-level configuration.

## Measure shape rules (apply to every visual/notes.md in this skill)

1. **Single string output.** The DAX measure must `RETURN` one concatenated string —
   no dynamic format strings evaluated outside the measure, no separate tooltip measure.
2. **Pre-format everything inside the measure.** Never rely on the visual to format
   numbers/dates — do it in DAX with `FORMAT()` before concatenating, since the HTML
   Content visual just renders the string.
3. **Escape correctly for context:**
   - Inline `<style>` blocks: plain CSS, no escaping needed.
   - Inline SVG embedded directly as markup (not as a data URI): quotes inside SVG
     attributes should use single quotes (`'`) so they don't collide with the DAX
     string's own double-quote delimiters.
   - Data URIs (`data:image/svg+xml;utf8,...`): special characters must be
     percent-encoded (`#` → `%23`, space → `%20`, etc.) since the string is a URI,
     not raw markup. See individual notes.md files for the exact colors already encoded.
4. **`VAR` names are per-file conventions only.** When adapting a template for a
   new report, the measure names inside `CALCULATE`/table references (e.g.
   `DimClub[Sport]`, `F_Properties[...]`) must be swapped for the user's actual
   model — that's the "key inputs needed" list in each visual's notes.md.
5. **Sizing is fixed-px by default.** Most templates here use fixed `width`/`height`
   card dimensions (e.g. 460×260) rather than `100%`/`100vh`, because the HTML Content
   visual's container sizing can clip or scroll unpredictably with wrapped content 3
   if a card is taller than the visual frame. Match the visual's frame size in the
   report to the template's fixed dimensions, or update both together.

## Placeholder convention used in `template.html` files

Templates in this skill are stored as the **DAX measure source**, not pre-rendered
HTML — Claude edits the DAX directly for the user's model. To make the swap points
unambiguous, every `VAR` that pulls from the user's model is wrapped as:

```
«MEASURE: description of what goes here»
```

e.g. `VAR _rev = «MEASURE: total revenue, numeric, not pre-formatted»`. When
generating a real measure for a user, replace each `«MEASURE: ...»` token with
their actual measure/column reference and delete the angle-bracket comment.
Tokens that reference dimension/table names to `SWITCH`/`CALCULATE` against use
the same bracket style, e.g. `«DIMENSION: category column, e.g. DimClub[Sport]»`.

## Known pitfalls (general — visual-specific ones live in each notes.md)

- **Hover-based interactions (flip/slide/blink cards) will not work in Power BI
  mobile or in exported PDF/PPT** — hover has no equivalent on touch. If the report
  needs to work on mobile, don't use a hover-reveal card; ask which visuals need a
  static fallback.
- **Font availability**: all templates default to `Segoe UI, sans-serif`. If the org
  doesn't have Segoe UI available in the rendering environment (some non-Windows
  Report Server setups), this silently falls back to the browser default sans-serif.
- **CONCATENATEX-built repeating cards** (e.g. country/category grid) have no
  pagination — if the dimension has a large number of members, the whole HTML
  string can get very large and slow to re-render on every filter change.
