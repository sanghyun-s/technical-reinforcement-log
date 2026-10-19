# MP#01 — Netflix "What We Watched" end-to-end EDA + strategy memo

## Activity
- **Skill:** r-analytics (end-to-end: dplyr + skimr + gt + ggplot + Quarto → published site)
- **Topic:** exploratory analysis of 61,892 viewing records (2023H1–2026H1); missing-data handling; a corporate strategy memo
- **Source:** Baruch STA 9750 Mini-Project #01
- **Mode:** Coursework → **Independent analysis** (my own R, my own analytical choices and memo)
- **Date:** 2026-10-16

## What I worked on
A complete query → profile → clean → aggregate → visualize → communicate pipeline on Netflix's
semi-annual "What We Watched" data, rendered through Quarto to a live GitHub Pages site. Profiled
the data with `skim()`; computed inline values (date range, distinct titles, franchise watch-hours,
Top-10 share, global-availability share); built `gt` tables (top title per period, consistent
performers, shows-vs-movies, live-vs-pre-taped); and wrote a strategy memo with a core recommendation.

**Live artifact:** https://sanghyun-s.github.io/STA9750-2026-FALL/mp01.html

## Independent attempt
Wrote the dplyr/ggplot/gt myself. The analytical *judgments* were mine and are the gradable part:
how to classify show-vs-movie from a naming heuristic (and flagging it as imperfect), what the
`views` column's 2023H1 gap means, and how to frame association-vs-causation in the memo.

## Assistance used
- **Course material** (the MP#01 spec) for the required questions.
- **AI** (this session) only to reorganize into the V2 activity format and extract defensible
  lines — it did not write the R or the memo.
- Verified my own numbers with independent routes (`skim` vs manual `summarize`, Top-10 share
  cross-checked against the per-period totals).

## Development layer — fluency
**Can now do that I couldn't before:**
- Profile an unfamiliar 60k-row dataset fast with `skim()` and *read the completeness column* —
  caught that `views` is 18,196/18,198 missing inside 2023H1 alone (a period artifact, not random).
- Build presentation-grade `gt` tables (not just console tibbles) and an iterated ggplot.
- Compute a **weighted share correctly**: Top-10 watch-time share as `sum(top) / sum(all)`, not a
  mean of per-title percentages.
- Separate *catalog share* from *watch-hour share* — globally-available titles are 35.1% of the
  catalog but 52.1% of watch hours, and say why that's **association, not causation**.

**Still shaky:** the show-vs-movie heuristic is naming-based and admittedly misclassifies some
long films / short series — I flagged it rather than hid it, but a cleaner classifier is open work.

**Reproduce cold?** **Partial** — the `skim`→`summarize`→`gt` flow yes; the full Quarto-site build
and the franchise-grouping regexes I'd want one more pass on.

## Technical mistake or misconception
The real lesson, consistent with my R join/pivot work: **a missing row is not a zero, and an
average silently divides by what arrived.** The 2023H1 `views` gap would quietly bias any
view-based metric, so I reported watch-*hours* (100% complete) for the headline numbers and treated
`views` as period-limited. Naming which column to trust for which claim is the analyst's job.

## Artifact / Evidence
Live published site (link above) + the Task 5/6 command set
([Task 5–6 dplyr/gt commands](./dplyr-aggregation-gt-tables_netflix-commands.md)) + this activity log.

## Defense layer — interview
**Can defend now:**
- *"No single title dominates Netflix viewing — the top title in any half-year is only 0.9–1.4% of
  watch hours, and the Top 10 is ~6%. The business story is a long tail, not a few megahits."*
- *"Globally-available titles are 35% of the catalog but 52% of watch hours — they over-index on
  viewing. I present that as an association, not causation: cross-border-designed content may just
  correlate with broad-appeal production, and the data can't separate the two."*
- *"I headline watch-hours, not views, because `views` is 99% missing in one period — reporting a
  view-based metric there would be a period artifact dressed as a trend."*

**Follow-up that could expose weakness:** "How robust is your show/movie split?" — honestly, it's a
naming heuristic with known misclassification; I'd want metadata, not string matching.

## Honest limit
Course-guided, not a from-scratch independent project — so it doesn't yet fill V2's
`applied-practice/end-to-end-analysis/` slot (that's reserved for a self-directed one). And the
classifier caveat above is real.

## Open thread
- A **self-directed** end-to-end analysis (different dataset, no spec) → `applied-practice/end-to-end-analysis/`.
- The natural bridge: do the same aggregation in **SQL** (`GROUP BY` period, `sum()/sum()` share) → first `sql/` activity.

## Self-narrative check
**Yes.** "I've shipped a published end-to-end analysis on 60k rows — profile, clean, aggregate,
visualize, and a defensible business memo" is a concrete analyst claim I couldn't make a month ago,
and it's the strongest single piece of Data-Analyst evidence in the repo.
