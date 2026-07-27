# Stacked Column (2-Series, Vertical)

**Use when:** the user wants each group shown as ONE bar whose height is the
group's total, split into 2 colored segments — as opposed to `clustered-column`
where the 2 series sit as separate side-by-side bars. Good when the group
totals themselves matter as much as the series breakdown.

**Depends on:** HTML Content by Daniel Marsh-Patrick.

**Related variants:** `clustered-column` (2 series side by side instead of
stacked), `clustered-bar` (horizontal, side by side), `stacked-bar-100`
(horizontal, forces every bar to 100% rather than showing true totals).

## Required inputs

Same measures as `clustered-column` (group list, Series A/B per group) plus a
derived `_GrpNTotal = SeriesA + SeriesB` per group, which becomes both the
label shown above each stack and the scaling reference (`_Max` = the largest
group total, not the largest single series value — see styling note).

## Styling conventions specific to this visual

- **`_Max` is computed from group TOTALS, not individual series values** —
  this is the key difference from `clustered-column`'s scaling. Don't reuse
  `clustered-column`'s per-series max here, it'll make stacks overflow the
  chart height.
- Series B renders on top of the stack with rounded top corners
  (`sc-seg-b{border-radius:6px 6px 0 0}`), Series A sits at the bottom with
  square corners. If the user wants the opposite series order, swap which
  segment gets the `sc-seg-a`/`sc-seg-b` rounding class, don't just swap colors.
- The group total is displayed as its own line **above** the stack
  (`.sc-total`), separate from the per-segment value labels inside each
  colored segment — keep both, they answer different questions (what's the
  group total vs. what's each segment's contribution).

## Known pitfalls / edge cases

- Same fixed/hand-written group list and non-negative-values limitations as
  `clustered-column`.
- **If a segment's height is very small relative to the total** (e.g. Series A
  is 2% of the group), its in-segment value label may not fit and can visually
  overflow/get clipped — for very lopsided splits, consider whether showing
  the value label only above the stack (like the group total) rather than
  inside tiny segments would read better; ask the user if their data is likely
  to have that kind of imbalance.
- Keep `.sc-chart` and `.sc-labels` group counts in lockstep, same pitfall as
  `clustered-column`.
