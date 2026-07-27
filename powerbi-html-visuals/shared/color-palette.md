# Shared Style Conventions

Extracted from patterns repeated across the existing templates. New visuals should
default to these unless the user asks for a different brand palette.

> **Status: draft.** This is a first pass based on the visuals audited so far
> (KPI cards, CF badges, sports/injury-analytics chart family, real-estate CF
> badges). As more visuals get added, update this file rather than letting new
> one-off palettes accumulate per-visual.

## Typography

- Font stack: `Segoe UI, sans-serif` — used in every template without exception.
- Property/ID-style monospace badges (e.g. `CF Property ID Col`) use
  `Courier New, monospace` instead — reserve monospace for identifier-like values,
  not general labels.

## Base UI colors (neutral shell)

| Role | Hex | Used in |
|---|---|---|
| Primary dark text / headers | `#1a2b4a` or `#1e2d3d` | almost all card titles |
| Secondary/muted text | `#8895a7` or `#999` | subtitles, "of $X target" labels |
| Card background (light theme) | `#FFFFFF` or `#f3f5f7` | card fronts |
| Card background (dark theme, card backs) | `#1e2d3d` or `#5C3D2E` | flip/slide backs |
| Border/divider | `#e8ecf0` or `#e0e4e9` | row separators, card borders |

## Status/tier colors (performance, risk, gauges)

Two competing conventions currently exist in the repo — **needs a decision**:

- Traffic-light convention (injury risk, revenue attainment tiers):
  - Green `#2ecc71` / `#22c55e` — good / low risk / target met
  - Blue `#4E95D9` / `#3B82F6` — on track / moderate-good
  - Orange `#e67e22` / `#F59E0B` — caution / moderate risk
  - Red `#c0392b` / `#D93025` — bad / critical risk
- Teal/amber convention (real-estate CF badges):
  - Teal `#089BAB` family — fast/positive
  - Amber `#f5a623` family — moderate
  - Soft red `#e07b7b` — slow
  - Grey `#cccccc` — stale/neutral/no-data

**Recommendation (pending confirmation):** treat traffic-light as the default for
any new "performance tier" visual, and keep the teal/amber scheme scoped to
real-estate-specific badges only, unless told otherwise.

## Gender/category comparison colors (seen in clustered/stacked charts)

- Female: dark navy gradient `#0d1b30 → #244181`
- Male: lighter blue gradient `#3a6bbf → #7aa8eb`

This is domain-specific to the sports/injury dataset the source charts came from —
**do not reuse for generic two-series comparisons** without checking with the user
first; pick a neutral two-series pair (e.g. `#244181` / `#7aa8eb` without the
gender association) unless the new report is genuinely comparing the same categories.

## Card sizing conventions

- Single KPI card: ~420–460px wide × 260–280px tall, fixed (not responsive)
- Small-multiple donut grid: 100×100 SVG per donut, 2-column CSS grid
- Bar-list rows: track height 8–10px, full-width flex container
