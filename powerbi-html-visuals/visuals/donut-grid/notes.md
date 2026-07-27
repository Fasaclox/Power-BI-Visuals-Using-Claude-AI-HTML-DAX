# Donut Grid (Small Multiples)

**Use when:** the user wants several independent categories compared side by
side, each as its own small donut showing that category's share of the overall
total — e.g. "a donut per injury cause showing what % of all injuries each
cause represents." Different from `donut-single`, where one donut is divided
into segments that together make up 100%.

**Depends on:** HTML Content by Daniel Marsh-Patrick.

**Related variant:** `donut-single` — one shared donut split into mutually
exclusive segments. Use this (`donut-grid`) instead when each category should
be visually independent/comparable rather than parts of one whole ring.

## Required inputs

| Token | What it must be |
|---|---|
| `_Total` | Shared denominator every category's % is measured against |
| `_CatA`, `_CatB`, ... | One measure per category — these do NOT need to sum to `_Total` (unlike donut-single's segments), since each card is independent |
| Category colors | One accent color per card's filled arc |
| Category labels | Card name text |
| Optional icon/emoji per card | Shown in the donut's center — delete the `<text>` element per card if not wanted |
| Title | Static header |

## Styling conventions specific to this visual

- **2-column CSS grid** (`grid-template-columns:1fr 1fr`) — an odd number of
  categories leaves the last card alone on its own row, which is expected and
  fine; don't force a 3rd column to "balance" it unless asked.
- Each donut's fill arc is computed independently via
  `stroke-dashoffset = circumference × (1 - pct/100)` — **this is a simpler,
  non-cumulative formula than `donut-single`'s stacked-segment math**, because
  each donut only ever shows one filled arc against its own empty track, not
  multiple stacked segments. Don't port `donut-single`'s running-offset-sum
  logic here by mistake — it's unnecessary and wrong for this pattern.
- Same `_Circ = 251.33` note as `donut-single`: recompute if the radius
  (`r=40`) changes.

## Known pitfalls / edge cases

- Since each card's percentage is against the SAME shared `_Total`, the
  percentages across all cards will generally NOT sum to 100% unless the
  categories happen to be a true partition — that's expected here (unlike
  donut-single) but worth clarifying with the user so they don't expect the
  cards to "add up."
- Fixed, hand-written category list — same limitation as other chart visuals
  in this skill; not suitable for a dynamic/unpredictable category count
  without restructuring.
- If categories vary a lot in magnitude, small-percentage cards will show a
  nearly-empty ring — confirm that's acceptable or whether the user wants a
  minimum visible arc floor (not currently in the template).
