> **Skill:** r-analytics (dplyr aggregation + gt tables, analytical judgment) · **Source:** Baruch STA 9750 MP #01 · **Mode:** Coursework (analysis) · **Date:** 2026-10-16
> Companion: [Netflix end-to-end EDA](./end-to-end-eda_netflix-watch-data_r-quarto.md)
> *Working commands with LOOK / RUN / CHECK structure for the Netflix mini-project. Original below, verbatim.*

---

# MP#01 — Task 5 and Task 6 Commands

**None of this has been executed.** I have no R in my environment, so every block is untested
against your actual data. Each question therefore has three parts:

- **LOOK** — run this first to see what you're about to operate on
- **RUN** — the computation
- **CHECK** — an independent route that would disagree if the computation is wrong

Where a question is genuinely ambiguous, I say so rather than picking silently. Those are
analytical choices you have to make and explain — the assignment says so explicitly.

Everything assumes `NETFLIX_DATA` is loaded with tidy lowercase names and that `scales`, `gt` and
`skimr` are attached from the `eda-checks` chunk.

---

## Before Task 5 — two structural facts to establish

These two answers change how several later questions should be written, so settle them first.

**LOOK:**

```r
# Does available_globally contain NA? (It will if the source had blanks:
# NA == "Yes" is NA, not FALSE.)
NETFLIX_DATA |> summarize(n_na = sum(is.na(available_globally)))

# Can one title appear twice within the same period?
NETFLIX_DATA |> count(title, period) |> filter(n > 1) |> arrange(desc(n))

# How many runtimes are NaN (titles the cleaning code could not impute)?
NETFLIX_DATA |> summarize(nan_runtime = sum(is.nan(runtime)),
                          na_runtime  = sum(is.na(runtime)))
```

If the second returns zero rows, a title appears at most once per period and the Task 6 grouping
logic below is safe. If it returns rows, tell me — several things change.

---

# Task 5 — Inline values

Each block assigns to a variable. You then reference that variable inline in your sentence, which
is what keeps the value out of your prose. Put these in the labelled chunks already in `mp01.qmd`.

## Q1 — Date range

**RUN:**

```r
q1_start <- min(NETFLIX_DATA$period_start)
q1_end   <- max(NETFLIX_DATA$period_end)
```

In your sentence, reference `q1_start` and `q1_end` inline, formatted with
`format(q1_start, "%B %Y")` if you want month names rather than ISO dates.

**CHECK:**

```r
range(NETFLIX_DATA$period)
```

Should be `"2023H1"` and `"2026H1"`, consistent with the dates above. Two different columns
agreeing is the point.

Note this is the range of *reporting periods*, not of release dates — `release_date` goes back
much further. Worth a clause in your sentence.

## Q2 — Distinct titles

**RUN:**

```r
q2_n_titles <- n_distinct(NETFLIX_DATA$title)
```

**CHECK:**

```r
NETFLIX_DATA |> distinct(title) |> nrow()     # same number, different route
nrow(NETFLIX_DATA)                            # much larger - rows are title-periods
```

The gap between those two is the thing to mention: 61,892 rows but far fewer distinct titles,
because most titles appear in several reporting periods.

## Q3 — Bridgerton universe

**LOOK FIRST — do not skip this:**

```r
NETFLIX_DATA |>
    filter(str_detect(title, "Bridgerton")) |>
    distinct(title) |>
    arrange(title) |>
    print(n = Inf)
```

Read that list before you sum anything. You're deciding what "the Bridgerton universe" means.
`Queen Charlotte: A Bridgerton Story` contains the string, so it's included — that's probably
right, but it's a choice. If anything in the list surprises you, that's your answer changing.

**RUN:**

```r
q3_bridgerton_hours <- NETFLIX_DATA |>
    filter(str_detect(title, "Bridgerton")) |>
    summarize(hours = sum(hours_viewed)) |>
    pull(hours)
```

**CHECK:**

```r
# Per-title breakdown; the parts must sum to the whole
NETFLIX_DATA |>
    filter(str_detect(title, "Bridgerton")) |>
    summarize(hours = sum(hours_viewed), .by = title) |>
    arrange(desc(hours))
```

**Caution:** `str_detect()` is case-sensitive. If you want it not to be, use
`str_detect(title, regex("bridgerton", ignore_case = TRUE))`. Run both and see whether the number
moves — if it does, the data has inconsistent capitalisation and that's worth knowing.

## Q4 — Stranger Things, the five real seasons

The trap question. Work in three steps.

**LOOK FIRST:**

```r
NETFLIX_DATA |>
    filter(str_detect(title, "Stranger Things")) |>
    distinct(title) |>
    arrange(title) |>
    print(n = Inf)
```

This is the whole question. You cannot write correct exclusions without seeing the exact strings.
He names three to drop — "Beyond Stranger Things", "Tales from '85", "Stranger Things: Fireplace" —
but the titles in the data may be spelled differently, and there may be others he didn't mention.

**RUN — sequential, one exclusion per statement, exactly as he instructs:**

```r
q4_data <- NETFLIX_DATA |>
    filter(str_detect(title, "Stranger Things")) |>
    filter_out(str_detect(title, "Beyond")) |>
    filter_out(str_detect(title, "Tales from")) |>
    filter_out(str_detect(title, "Fireplace"))

q4_hours <- q4_data |> summarize(hours = sum(hours_viewed)) |> pull(hours)
```

Adjust those three patterns to match what the LOOK step actually showed you.

**Why separate statements.** Combining them —
`filter_out(str_detect(title, "Beyond") | str_detect(title, "Tales from"))` — means
`!(A | B)`, which by De Morgan is `!A & !B`. People consistently read it as `!A | !B`, which
excludes almost nothing. Same error from Lab 4. Three separate statements can't be misread.

**CHECK — the most important one on this task:**

```r
q4_data |> distinct(title) |> arrange(title) |> print(n = Inf)
```

**You should see exactly five titles, and you should recognise all five as real seasons.** If you
see four, an exclusion pattern is too greedy. If you see six, one companion title is still in.
Counting the rows is not enough; read the names.

**On `filter_out()`:** it's new in dplyr 1.2.0 and I haven't used it. It should keep rows where the
condition is not TRUE. If it errors or behaves unexpectedly, `filter(!str_detect(title, "Beyond"))`
is the equivalent here — the two differ only in how they treat `NA` conditions, and `str_detect()`
on non-missing titles never returns `NA`, so either is safe in this specific case.

## Q5 — Share of 2026H1 watch time held by the top 10

**Ambiguity to settle:** "percentage of total watch time" almost certainly means total watch time
*within 2026H1*. It could mean the whole seven-period dataset. Pick the within-period reading —
it's the useful question — and say so in your sentence.

**RUN:**

```r
h1_2026 <- NETFLIX_DATA |> filter(period == "2026H1")

q5_total  <- sum(h1_2026$hours_viewed)
q5_top10  <- h1_2026 |> slice_max(hours_viewed, n = 10)
q5_share  <- sum(q5_top10$hours_viewed) / q5_total
```

**CHECK:**

```r
nrow(q5_top10)                                   # MUST be exactly 10
q5_top10 |> select(title, hours_viewed)          # read the titles
```

**The ties trap:** `slice_max()` defaults to `with_ties = TRUE`, so if titles 10 and 11 have
identical `hours_viewed` you get 11 rows and your "top 10" share is wrong. Given hours are rounded
to the nearest 100,000, ties are plausible. If `nrow()` isn't 10, add `with_ties = FALSE` and say
in your text how you broke the tie.

## Q6 — Fraction globally available

**Two ambiguities**, both of which you should resolve explicitly:

1. "By title" — distinct titles, or title-period rows? A title in six periods would otherwise
   count six times.
2. Does `available_globally` ever disagree with itself for the same title across periods?

**LOOK FIRST:**

```r
NETFLIX_DATA |> summarize(n_na = sum(is.na(available_globally)))

NETFLIX_DATA |>
    summarize(n_distinct_values = n_distinct(available_globally), .by = title) |>
    filter(n_distinct_values > 1) |>
    nrow()
```

If that second returns 0, every title is consistently flagged and the distinction doesn't matter.
If it returns a positive number, some titles changed availability status between periods — which
is genuinely interesting and worth a sentence.

**RUN:**

```r
# By title (distinct titles)
q6_by_title <- NETFLIX_DATA |>
    distinct(title, available_globally) |>
    summarize(share = mean(available_globally)) |>
    pull(share)

# Weighted by hours viewed
q6_by_hours <- NETFLIX_DATA |>
    summarize(share = sum(hours_viewed[available_globally]) / sum(hours_viewed)) |>
    pull(share)
```

**If the NA check above returned anything non-zero**, both lines need `na.rm = TRUE` and you need
to say in your text how many rows you dropped.

**CHECK:**

```r
NETFLIX_DATA |> count(available_globally)
NETFLIX_DATA |> summarize(hours = sum(hours_viewed), .by = available_globally)
```

Compute both shares by hand from those two tables and confirm they match. `mean()` of a logical is
a proportion — the Week 5 lesson — so the first number should equal `TRUE / (TRUE + FALSE)` from
`count()`.

The **gap between the two figures** is the real finding and the thing to write about: if globally
available titles are a minority by count but a majority by hours, Netflix Originals punch well
above their weight in the catalog. Format both with `percent(x, accuracy = 0.1)`.

---

# Task 6 — Tables, formatted with `gt`

Each of these builds a tibble and then formats it. **Read the `gt` introduction on the assignment
page first** — the collapsed blue "Making Attractive Tables in R" callout. The `gt` calls below are
a starting point, not polished output, and "Tables & Document Presentation" is a rubric element
you're fully responsible for.

## Q1 — Top title in each period

**RUN:**

```r
tbl_top_per_period <- NETFLIX_DATA |>
    summarize(period_total = sum(hours_viewed), .by = period) |>
    left_join(
        NETFLIX_DATA |>
            slice_max(hours_viewed, n = 1, by = period) |>
            select(period, title, hours_viewed),
        join_by(period)
    ) |>
    mutate(share = hours_viewed / period_total) |>
    arrange(period)

tbl_top_per_period
```

That's your multi-table join from PA #05 — an aggregate joined back to the detail it came from.

**CHECK:**

```r
nrow(tbl_top_per_period)    # MUST be 7, one row per period
```

If it's more than 7, two titles tied for the top in some period and `slice_max` returned both.
Add `with_ties = FALSE` and note the tie.

**FORMAT:**

```r
tbl_top_per_period |>
    select(period, title, hours_viewed, share) |>
    gt() |>
    fmt_number(columns = hours_viewed, decimals = 0, suffixing = TRUE) |>
    fmt_percent(columns = share, decimals = 1) |>
    cols_label(period = "Period", title = "Top Title",
               hours_viewed = "Hours Viewed", share = "Share of Period") |>
    tab_header(title = "[your table title]")
```

## Q2 — Catalog size over time

**RUN:**

```r
tbl_catalog_size <- NETFLIX_DATA |>
    summarize(n_titles      = n_distinct(title),
              runtime_hours = sum(runtime, na.rm = TRUE) / 60,
              hours_viewed  = sum(hours_viewed),
              .by = period) |>
    arrange(period)

tbl_catalog_size
```

**`runtime` is in minutes** — the professor's code converts `hh:mm` to minutes with
`60 * hour(...) + minute(...)`. Hence the `/ 60`. Confirm:

```r
summary(NETFLIX_DATA$runtime)
```

A median in the hundreds means minutes. A median under 10 would mean I've misread it — tell me.

The `na.rm = TRUE` is doing real work here, because of the `NaN` runtimes from the imputation.
Report how many you dropped.

## Q3 — Most consistent performers

**RUN:**

```r
tbl_consistent <- NETFLIX_DATA |>
    summarize(n_periods  = n(),
              mean_hours = mean(hours_viewed),
              min_hours  = min(hours_viewed),
              sd_hours   = sd(hours_viewed),
              .by = title) |>
    filter(n_periods == 7) |>
    mutate(cv = sd_hours / mean_hours) |>
    arrange(desc(min_hours)) |>
    slice_head(n = 10)

tbl_consistent
```

**The rule encoded here, which you must state and defend in your text:** present in all seven
periods, ranked by the *worst* period's viewership. "Consistent" means a high floor, not a high
average — a title with one enormous period and six quiet ones has a high mean but isn't comfort
food.

`cv` is the coefficient of variation, standard deviation relative to mean. Lower means steadier.
Ranking by `cv` instead gives a different and equally defensible answer — try both and pick one,
then explain why.

**CHECK:**

```r
NETFLIX_DATA |> filter(title %in% tbl_consistent$title) |>
    select(title, period, hours_viewed) |>
    arrange(title, period) |>
    print(n = Inf)
```

Eyeball it. Each title should have seven rows with no collapse to near-zero. If one does, your
filter isn't capturing "consistent."

## Q4 — Shows versus movies

**The heuristic is yours to design.** This starting point uses his two suggestions. The `NaN`
runtimes make it messier than it looks.

**LOOK FIRST:**

```r
# How many titles would each rule catch on its own?
NETFLIX_DATA |>
    distinct(title, runtime) |>
    summarize(has_season  = sum(str_detect(title, regex("season", ignore_case = TRUE))),
              over_4h     = sum(runtime > 240, na.rm = TRUE),
              both        = sum(str_detect(title, regex("season", ignore_case = TRUE)) &
                                runtime > 240, na.rm = TRUE),
              runtime_nan = sum(is.nan(runtime)))
```

The overlap between the two rules tells you whether they agree. Where they disagree is where your
heuristic is doing real work, and that's what you write about.

**RUN:**

```r
tbl_shows_movies <- NETFLIX_DATA |>
    mutate(is_show = case_when(
        str_detect(title, regex("season", ignore_case = TRUE)) ~ TRUE,
        !is.na(runtime) & !is.nan(runtime) & runtime > 240     ~ TRUE,
        is.na(runtime) | is.nan(runtime)                       ~ NA,
        TRUE                                                   ~ FALSE
    )) |>
    summarize(hours = sum(hours_viewed), .by = c(period, is_show)) |>
    mutate(share = hours / sum(hours), .by = period) |>
    arrange(period, is_show)

tbl_shows_movies
```

**`case_when` rather than `|` deliberately.** With a plain `A | B`, a `NaN` runtime makes
`runtime > 240` evaluate to `NA`, and `FALSE | NA` is `NA` while `TRUE | NA` is `TRUE` — so some
titles get classified and others silently vanish, depending on which rule fired first. The
`case_when` makes "I don't know" an explicit third category you can count and report, rather than
a hole.

**CHECK:**

```r
tbl_shows_movies |> summarize(total_share = sum(share), .by = period)
```

Every period must sum to exactly 1. If it doesn't, rows went missing.

Also report the size of the `NA` category — unclassifiable titles are a limitation worth stating,
not hiding.

## Q5 — Live events versus pre-taped

**LOOK FIRST:**

```r
NETFLIX_DATA |> count(period, live_event)
```

Live events are new for Netflix, so expect few or none in the early periods. If `live_event` is all
`FALSE` before some period, pooling across all seven years would understate live performance badly.
That's the analytical choice to make here.

**RUN:**

```r
# Average performance
tbl_live_avg <- NETFLIX_DATA |>
    summarize(n            = n(),
              mean_hours   = mean(hours_viewed),
              median_hours = median(hours_viewed),
              total_hours  = sum(hours_viewed),
              .by = live_event)

# Top tier: the best 20 of each kind
tbl_live_top <- NETFLIX_DATA |>
    slice_max(hours_viewed, n = 20, by = live_event) |>
    summarize(mean_top20_hours = mean(hours_viewed), .by = live_event)

tbl_live_avg
tbl_live_top
```

**CHECK:**

```r
NETFLIX_DATA |> slice_max(hours_viewed, n = 5, by = live_event) |>
    select(live_event, period, title, hours_viewed) |>
    arrange(live_event, desc(hours_viewed))
```

Read the titles. The top live events should be recognisable sporting events. If they aren't, the
`live_event` flag isn't capturing what you think — his detection rule is `str_detect(Runtime,
"\\d+:\\d+\\*")`, an asterisk on the runtime, which is a proxy rather than a label.

**Mean versus median matters here.** A handful of enormous live events will drag the mean up while
the median stays low. Report both; the gap is the finding.

---

## General notes

**Storing values for inline use.** Every Task 5 answer assigns to a variable so your sentence can
reference it. Don't inline a whole pipeline — compute in the chunk, reference the name in the text.

**Formatting.** `comma()` for counts, `percent(x, accuracy = 0.1)` for shares, `number(x,
scale = 1e-9, suffix = "B")` for very large hour counts. From `scales`, loaded in your EDA chunk.

**If any block errors**, send me the error and the output of the LOOK step above it. The LOOK steps
exist so that when something is wrong we find out at the point of the wrong assumption rather than
three steps later.
