# Narrative Summary (Dynamic Sentence)

**Use when:** the user wants a plain-language sentence that auto-updates with
slicer selection — e.g. "Overall listings went from X in 2020 to Y in 2024,
making listings up by Z properties (12.3%) since 2020." This is the ONE
visual in this skill that is **not HTML** — it's plain text, meant for a
Card/Multi-row Card visual or a text box, not the HTML Content visual.

**Depends on:** nothing custom. Any native Power BI visual that displays a
single text measure (Card, Multi-row Card, or even a plain Table cell).

## Required inputs

| Token | What it must be |
|---|---|
| Period dimension | The field a slicer selects on (e.g. `D_Date[Year]`) |
| Baseline period | A fixed reference point everything compares against (e.g. `2020`) — must be a valid member of the period dimension |
| Count/sum measure | What's being tracked over time (e.g. `COUNTROWS(F_Properties)`, or any other measure) |
| Unit label | The noun being counted, used in both sentence templates (e.g. "properties," "orders," "signups") |
| Verb (baseline sentence only) | e.g. "listed," "sold," "recorded" |
| Sentence templates | The two RETURN branches' wording — customize these to match tone/phrasing the user wants, this is the most "written by hand" part of this measure |

## Styling conventions specific to this visual

- No CSS — this is a DAX-only text measure. If the user wants this styled
  (bold numbers, colored arrows), that requires either (a) switching to a
  measure that returns HTML and pairing it with HTML Content after all, or
  (b) using Power BI's native conditional text formatting on the Card visual.
  Ask which they want rather than assuming plain text is fine.
- `▲`/`▼` are literal Unicode characters, not icons — they inherit whatever
  font/size the hosting Card visual uses.

## Known pitfalls / edge cases

- **`BaseCount = 0` breaks `PctChange`** — `DIVIDE` with a 0 denominator
  returns `BLANK()` here (no third argument supplied), so `AbsPct` would show
  blank rather than an error, but confirm that's the desired fallback; if the
  user wants a specific fallback message for a zero baseline ("no baseline
  data available"), that needs an explicit `IF(BaseCount = 0, ..., ...)` branch.
- **Two sentence branches, not one** — when `ComparePeriod = BasePeriod`
  (i.e. no change to compare, viewer has selected the baseline year itself),
  the measure returns a DIFFERENT sentence template (the "baseline" one) than
  when comparing two different periods. Don't collapse these into one
  template — the baseline case has no meaningful "change" to describe.
- **`MAXX(ALL(...), ...)` as the no-selection fallback** assumes "most recent
  period" is the right default when nothing is sliced — confirm that's what
  the user wants (vs., say, defaulting to the current period based on
  `TODAY()`, which would need different logic).
- If the period dimension format differs from a plain number (e.g. a
  "2024-Q1" text label instead of a numeric year), string vs. numeric
  comparison (`ComparePeriod = BasePeriod`) still works but sentence
  formatting (`FORMAT(BaseCount, "#,##0")` etc.) doesn't need to change —
  only the period value itself does.
