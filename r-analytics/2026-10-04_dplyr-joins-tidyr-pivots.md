# dplyr Multi-Table Joins + tidyr Pivots — validation-first data combination

## Activity
- **Skill:** r-analytics (dplyr joins, tidyr pivots) — transferable to SQL joins / pandas merge
- **Topic:** `inner/left/right/full/anti/semi_join`, `join_by`, compound keys, `pivot_wider/longer`, `rowwise` + `c_across`
- **Source:** Baruch STA 9750 (PA #05, Lecture 5 pre-class) — course is metadata; the skill is the point
- **Mode:** Coursework (course-guided, run cold in my own RStudio console before the quiz)
- **Date:** 2026-10-04

## What I worked on
Ran every block of a multi-table-`dplyr` walkthrough in the console (not just reading the posted
solutions), covering: the six join types with explicit `join_by()`; one-to-many joins; the
missing-rows trap; non-unique keys (many-to-many); compound (multi-column) joins; `pivot_wider` /
`pivot_longer`; and three ways to compute a row-wise average (`group_by`, `rowwise`, and the broken
no-grouping version). Nine distinct "traps" documented, each confirmed by printing the result.

## Independent attempt
Ran each block and **predicted row/column counts before pressing Enter**, then checked. Got the
boundary trap (exactly-70 average → "D", not "C") right by hand. The two that genuinely surprised me
on first run: deleted rows *raising* the average, and the ungrouped `mean(c_across())` giving every
student the identical class average. Both printed a clean tibble with no error.

## Assistance used
- **Course material** (the walkthrough) for the exercise sequence and the "why" commentary.
- **No AI for the R itself.** AI (this session) only reorganized my own notes into the V2 activity
  format and flagged the cross-skill through-line — it did not write or explain the dplyr.
- Version gotcha I verified myself: `join_by()` needs dplyr ≥ 1.1.0 (`packageVersion("dplyr")`);
  the older syntax is `by = c("name" = "artist")`.

## Development layer — fluency
**Can now do that I couldn't before:**
- Choose the right join by *which rows survive*: `inner` (both), `left`/`right` (keep one side),
  `full` (keep all), `anti` (left rows with **no** match — and it's **not symmetric**), `semi`
  (filter the left table, never adds columns/rows).
- Write `join_by(a == b)` **explicitly every time** instead of trusting the `Joining with by = …`
  guess message (it's a message, not a warning — it scrolls away).
- Build a **compound join** `join_by(day==day, month==month, year==year)` and know the condition
  list is an **AND** (like stacked `filter()` conditions), not an OR.
- `pivot_wider(id_cols, names_from, values_from)` to go long→wide, and use `values_fill = 0` to
  make a deliberate decision about what a missing cell *means*.
- Compute a row-wise average correctly with `rowwise()` + `c_across(A:C)`.

**Still shaky:** `pivot_longer` round-tripping (ran it once, didn't fully internalize `cols`/
`names_to`/`values_to`); `relationship = "one-to-many"` as a hard guard (saw it, haven't used it in
a real pipeline).

**Reproduce cold?** **Partial** — the six joins, `join_by`, and `pivot_wider` yes; the `rowwise` +
`c_across` average and the compound-join syntax I'd want one more cold pass on.

## Technical mistake or misconception (the real content)
Three separate "ran clean, answered wrong" cases, all confirmed by running them:
1. **Deleted rows raised the grade.** A missing assignment isn't a zero — it's *absent*, so `mean()`
   divides by fewer rows. Hunter went C→B. There's no `NA` to find; the rows never existed.
2. **Many-to-many joins inflate silently.** A duplicated `id` (2 students, 3 grade rows for that key)
   → 2×3 = 6 rows for one key, a table **longer than either input**. dplyr warns *once* and keeps going.
3. **`mutate(mean(c_across(A:C)))` with no grouping** gives every row the identical class average —
   `mean` collapses everything, `mutate` recycles the one value down all rows.

**Correction / diagnostic habits adopted:** `nrow()` before vs after a join; `df |> count(key) |>
filter(n > 1)` to catch a non-unique key *before* joining; **`pivot_wider` to turn implicitly-absent
rows into visible `NA`s**; `rowwise()` when there's no clean row identifier.

## Artifact / Evidence
This document + the full annotated walkthrough with all nine traps (source lives in the course
materials). The six checks worth memorizing are captured in the walkthrough's summary table.

## Defense layer — interview
**Can defend now:**
- *"Before aggregating after a join I check cardinality — `nrow` before/after and `count(key)` for
  duplicates — because a one-to-many or many-to-many join silently inflates totals, and the tool
  only warns once."*
- *"A missing row is not a zero. After a join I pivot wider to turn implicitly-absent rows into
  visible `NA`s, then decide explicitly what absence means (`values_fill = 0` vs leave `NA`) —
  that judgment belongs to me, not to `mean()`."*

**Follow-up that could expose weakness:** "Show me the SQL equivalent" — I can describe it
(LEFT JOIN + the same grain/duplication risk) but haven't *written* it cold yet. That's the bridge
to the `sql/` track.

## Honest limit
I should **not** claim fluency in `pivot_longer` or production-grade join guards
(`relationship=`) yet — seen once, not reproduced cold. And this was *course-guided*, not a cold
solve on unfamiliar data.

## Open thread
- Next r-analytics activity: redo Sections 3, 5, 7 **cold** on different tables (promotes "Partial"
  → "Yes").
- Natural bridge: the same join-cardinality lesson in **SQL** → first `sql/` activity (write the
  LEFT JOIN + a `GROUP BY` that doesn't double-count).
- Team-project relevance: weather↔NYPD data joins on a 5-column date key = textbook compound-join
  (Trap 7); use `make_date()` to collapse it to one key first.

## Self-narrative check
**Yes — a small but real one.** "I validate join cardinality before aggregating" is a concrete,
defensible analyst habit I didn't have a week ago, and it's the same principle as my Python
empty-group guard and R recycling lesson — so it strengthens the *"I check for silent data errors
across languages"* story, not just an R fact.
