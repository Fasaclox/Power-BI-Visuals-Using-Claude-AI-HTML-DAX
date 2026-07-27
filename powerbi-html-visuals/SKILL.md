---
name: powerbi-html-visuals
description: Write the HTML/CSS/DAX code for custom Power BI visuals — KPI cards, gauges, donuts, bar/column charts, conditional-format status badges, category card grids, trend lines, and narrative summary text. Produces a ready-to-use DAX measure (and binding instructions) that the user then places into a visual themselves; it does not build or configure the visual in their report. Use when the person asks for a Power BI visual by type ("KPI card", "gauge", "donut chart", "status badge") or by intent ("progress to target", "color-code this column", "compare two groups"), or mentions binding a measure to the HTML Content custom visual or conditional formatting via data URI/Image URL. The user must state the kind of visual they want so the right code can be written.
---

# Power BI HTML Visuals

Use this skill whenever someone asks for a Power BI visual built from HTML/CSS/DAX
(KPI cards, gauges, donuts, custom bar/column charts, conditional-format badges,
country/category card grids, etc.) — whether they name a specific visual style or
just describe what they want to show ("progress to target", "a status badge for
this column", "compare two groups side by side").

## What this skill does (and doesn't) do

**This skill only writes code — it does not build the visual in Power BI.** The
deliverable is a DAX measure (returning an HTML/CSS or SVG string) that the user
then places into a visual **themselves**, inside their own report. Claude does not
have access to their Power BI file and cannot create, place, size, or configure any
visual for them.

So the output of this skill is always:
1. The finished DAX measure text (built from the matching `template.html`), and
2. Short setup notes telling the user which visual to drop it into and how to bind
   it (from the relevant `shared/*-binding.md` doc) — so they can wire it up on
   their side.

Because Claude is writing code blind to the actual report, **the user must state
the kind of visual they want** — either by name ("KPI card with a target", "100%
stacked bar", "status badge") or by intent ("progress to target", "compare two
groups"). Without that, there's no way to know which template/DAX shape to produce.
If the request doesn't map cleanly to a row in the decision table below, ask the
user to pick one rather than guessing.

## How to use this skill

1. Find the matching row in the **decision table** below by user intent.
2. Open **only** that visual's `visuals/<name>/notes.md` — it has the full DAX
   measure template, required inputs, and gotchas. Don't load other visuals'
   notes.md files unless the request genuinely spans more than one visual.
3. Check `shared/html-visual-binding.md` (full-canvas HTML cards) or
   `shared/conditional-format-binding.md` (single-cell badges) for the binding
   mechanics common to that visual's family — which custom visual it needs (if
   any), the placeholder token convention, and general pitfalls not repeated in
   every notes.md.
4. Check `shared/color-palette.md` before inventing new colors — reuse the
   existing tier/status conventions unless the user has their own brand palette.
5. Ask the user for the key inputs listed below (measure names, dimension/column
   names, thresholds) rather than guessing table/column names — every template
   uses `«MEASURE: ...»` / `«DIMENSION: ...»` placeholder tokens exactly where a
   real binding is needed.
6. If a visual's notes.md flags an ambiguous edge case (e.g. which of two similar
   sub-patterns applies), ask the user before picking one.
7. **Deliver the finished DAX measure plus binding instructions.** Replace every
   `«MEASURE: ...»` / `«DIMENSION: ...»` / `«LABEL: ...»` token with the user's real
   references, then hand back the complete measure in a code block, followed by a
   short "how to bind this in your report" note from the relevant `shared/*-binding.md`
   doc (which visual to add it to, the data-category/field-well setting, and any
   sizing to match). The user does the placing in Power BI — Claude only supplies
   the code and the instructions.

## Two visual families (different binding mechanism — see relevant shared doc)

The user picks the visual container on their side; these notes tell them which one
each template needs and how to bind the measure into it.

- **Full-canvas HTML cards** — require the **HTML Content** custom visual (by
  Daniel Marsh-Patrick). One measure returns a complete HTML/CSS string.
- **Conditional-format badges** — no custom visual needed. A measure/column
  returns a `data:image/svg+xml` URI, bound via Power BI's native Image URL data
  category on a table/matrix column.

## Decision table

| User intent (examples) | Visual | Key inputs needed | notes.md | Status |
|---|---|---|---|---|
| "KPI card with a target and hover detail", "progress to revenue target" | `kpi-card-target-slide` | current value, target value, attainment %, gap | [visuals/kpi-card-target-slide/notes.md](visuals/kpi-card-target-slide/notes.md) | ✅ Ready |
| Same as above but wants a 3D flip instead of a slide | `kpi-card-target-flip` | current value, target value, attainment %, gap | [visuals/kpi-card-target-flip/notes.md](visuals/kpi-card-target-flip/notes.md) | ✅ Ready |
| Single KPI, no target — just value + change vs. prior period | `kpi-card-simple-blink` | current value, prior-period value/variance | [visuals/kpi-card-simple-blink/notes.md](visuals/kpi-card-simple-blink/notes.md) | ✅ Ready |
| "Status badge for this column", "color-code days on market / rating / category" | `cf-status-badge` | column/measure to classify, tier thresholds or category list, colors | [visuals/cf-status-badge/notes.md](visuals/cf-status-badge/notes.md) | ✅ Ready |
| "Row of yes/no amenity icons" | `cf-icon-status-row` | list of boolean columns to show as icons | [visuals/cf-icon-status-row/notes.md](visuals/cf-icon-status-row/notes.md) | ✅ Ready |
| "Pill-style category label in a table" (Customer/Investor/Partner style) | `cf-status-badge` (fixed-width categorical variant) | category list + one color per category | [visuals/cf-status-badge/notes.md](visuals/cf-status-badge/notes.md) | ✅ Ready (see "worked examples") |
| "Card grid, one per country/category, with a split metric" | `category-card-grid` | grouping dimension, total measure, 2-way split measure | [visuals/category-card-grid/notes.md](visuals/category-card-grid/notes.md) | ✅ Ready |
| "100% stacked horizontal bar by category" | `stacked-bar-100` | category list (fixed), sub-category breakdown measure | [visuals/stacked-bar-100/notes.md](visuals/stacked-bar-100/notes.md) | ✅ Ready |
| "Horizontal bar list with a track, ranked/sorted" | `horizontal-bar-list` | category list, single measure, optional total for %s | [visuals/horizontal-bar-list/notes.md](visuals/horizontal-bar-list/notes.md) | ✅ Ready |
| "Grouped/clustered bars, 2 series side by side" | `clustered-bar` | 2 series measures, category list | [visuals/clustered-bar/notes.md](visuals/clustered-bar/notes.md) | ✅ Ready |
| "Grouped/clustered vertical columns" | `clustered-column` | 2 series measures, category list | [visuals/clustered-column/notes.md](visuals/clustered-column/notes.md) | ✅ Ready |
| "Stacked vertical columns" | `stacked-column` | 2 series measures, category list | [visuals/stacked-column/notes.md](visuals/stacked-column/notes.md) | ✅ Ready |
| "Donut chart, single, multi-segment" | `donut-single` | 3+ mutually-exclusive segment measures + total | [visuals/donut-single/notes.md](visuals/donut-single/notes.md) | ✅ Ready |
| "Grid of small donuts, one per category" | `donut-grid` | category list, per-category measure + shared total | [visuals/donut-grid/notes.md](visuals/donut-grid/notes.md) | ✅ Ready |
| "Line/area trend, switchable metric" | `trend-line-field-param` | Field Parameter (pre-built in model), one measure per switchable option, period count/grain | [visuals/trend-line-field-param/notes.md](visuals/trend-line-field-param/notes.md) | ✅ Ready |
| "Dynamic narrative sentence describing a trend" | `narrative-summary` | comparison measure, base period, comparison period — plain text, no HTML | [visuals/narrative-summary/notes.md](visuals/narrative-summary/notes.md) | ✅ Ready |
| "Stat comparison card (e.g. average vs median, skew)" | `stat-comparison-card` | 1-2 related measures (avg/median or similar pair) per metric | [visuals/stat-comparison-card/notes.md](visuals/stat-comparison-card/notes.md) | ✅ Ready — ⚠️ has a flagged possible encoding bug carried over from the source, see notes.md before reuse |

## Status legend

- ✅ **Ready** — template + notes.md complete, safe to use directly (check for
  any ⚠️ caveat noted in the table above first).
- 🚧 **Planned** — identified but not yet built. None currently outstanding —
  this legend stays in place in case new visual requests get triaged in
  before being built out.

## Shared references

- [shared/html-visual-binding.md](shared/html-visual-binding.md) — HTML Content
  visual setup, measure-shape rules, placeholder token convention, general pitfalls
- [shared/conditional-format-binding.md](shared/conditional-format-binding.md) —
  Image URL data category setup, percent-encoding rules, sizing
- [shared/color-palette.md](shared/color-palette.md) — fonts, base colors, tier/status
  color conventions, sizing defaults
