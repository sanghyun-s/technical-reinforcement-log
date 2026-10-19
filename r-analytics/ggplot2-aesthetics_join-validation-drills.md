> **Skill:** r-analytics (ggplot2 aesthetics + dplyr join/aggregation validation) · **Source:** Baruch STA 9750 PA #06 quiz · **Mode:** Coursework · **Date:** 2026-10-16
> Companion: [ggplot2 iterative plot refinement](./ggplot2_iterative-plot-refinement.md)
> *🔁 Transferable: the join-cardinality + "sum ÷ sum, not mean of percentages" + scale-check traps are language-universal. Original quiz prep below, verbatim.*

---

# STA 9750 PA #06 Quiz — Working Commands and Traps

**Due Tuesday 20 October, 6:00 PM.** Two sections: Q5–Q14 need R against the instructor's two CSVs;
Q15–Q19 are ggplot2 concept questions answerable without running anything.

**What I can and cannot do here.** Q15–Q19 I answer below with reasoning, and I checked the
ggplot2 documentation rather than working from memory. Q5–Q14 depend on data I cannot reach from
this session, so what follows is the command for each plus the specific way each one can run clean
and give a wrong number. Run them and read the output yourself — that is the arrangement that keeps
a repeat of the `binomial()` link mistake off this quiz.

---

## Part A — Q15 to Q19 (answer these now)

### Q15 — "It is required to load `ggplot2` with `library()` before use, even if `tidyverse` has already been loaded."

**FALSE.**

`ggplot2` is one of the core tidyverse packages. `library(tidyverse)` attaches it along with `dplyr`,
`tidyr`, `readr`, `purrr`, `tibble`, `stringr`, `forcats` and `lubridate`. You have seen this yourself
all semester — you call `read_csv()` and `mutate()` after loading only `tidyverse`.

Note the phrasing trap: the statement is about whether it is *required*, not whether it is *allowed*.
Loading it again is harmless and common in scripts; it just is not necessary.

### Q16 — Which aesthetic controls the color of a `geom_line()`?

**`color`.**

Options were fill / hue / color / palette. `hue` and `palette` are not aesthetics at all — they are
arguments to *scale* functions (`scale_color_hue()`, `scale_color_brewer(palette = 2)`). That
distinction is exactly what stage 7 of the reading was about: the aesthetic is the mapping, the scale
decides which colours it uses.

Verified against the documentation — `geom_path()`/`geom_line()` understand **x, y, alpha, colour,
group, linetype, linewidth**.

### Q17 — Which aesthetic controls the color of the **interior** of a `geom_line()`?

**`fill`** — with a caveat you should know about.

The intended lesson is the `color` / `fill` pair: **`color` is the boundary, `fill` is the interior.**
Q16 and Q17 are built as a matched pair to test exactly that, and `fill` is the only option left
that is an aesthetic at all.

**The caveat:** strictly speaking a line has no interior, and the documented aesthetic list for
`geom_line()` does **not** include `fill`. The question stem almost certainly should have said
`geom_bar()`, `geom_area()` or `geom_ribbon()` — a geom with an inside. Given the four options
offered, `fill` is still the answer to select; nothing else fits. Just do not come away believing
`geom_line()` has a fill aesthetic, because Q18 depends on knowing which geoms do.

### Q18 — Which can be used to disable (remove) the outer boundary of a `geom_bar()`?

**`color = NA`.**

A bar has both: `fill` paints the interior, `color` draws the outline around it. To remove the
outline you set the outline aesthetic to `NA`. `fill = NA` would hollow out the bar and leave the
outline — the opposite. `hue` and `palette` are scale arguments, not aesthetics, same as in Q16.

Worth testing once so the pair sticks:

```r
ggplot(penguins, aes(x = species)) + geom_bar(fill = "steelblue", color = "red")   # both visible
ggplot(penguins, aes(x = species)) + geom_bar(fill = "steelblue", color = NA)      # outline gone
ggplot(penguins, aes(x = species)) + geom_bar(fill = NA, color = "red")            # interior gone
```

### Q19 — Which aesthetic controls transparency / opacity?

**`alpha`.**

Standard across the whole grammar. `trans` and `opac` are not R functions or aesthetics; `width` is
a real argument for bars and boxplots but controls size, not opacity. `alpha` runs 0 (invisible) to
1 (opaque), and it is what you would use on your crime-track scatter plots where points overlap.

---

## Part B — Q5 to Q14, the data questions

### Setup — run this first

```r
library(tidyverse)

TEST_RESULTS <- read_csv("https://michael-weylandt.com/STA9750/schools_test_results.csv")
SCHOOL_INFO  <- read_csv("https://michael-weylandt.com/STA9750/schools_covariates.csv")

glimpse(TEST_RESULTS)
glimpse(SCHOOL_INFO)
```

Then four orientation checks. **Do not skip these** — three of the questions below turn on what they
show, and I am working from the column names the quiz itself mentions rather than from the files.

```r
# 1. What links the two tables? Q8, Q11-Q14 all need a join.
intersect(names(TEST_RESULTS), names(SCHOOL_INFO))

# 2. Exact spelling of the category values you will filter on.
TEST_RESULTS |> count(SUBGROUP_NAME)    |> print(n = Inf)
TEST_RESULTS |> count(ASSESSMENT_NAME)  |> print(n = Inf)
SCHOOL_INFO  |> count(REGION)           |> print(n = Inf)
SCHOOL_INFO  |> count(DISTRICT_TYPE)    |> print(n = Inf)

# 3. SCALE CHECK -- is PERCENT_PROFICIENT 0-100 or 0-1? Q10-Q13 are wrong by 100x if you guess.
summary(TEST_RESULTS$PERCENT_PROFICIENT)

# 4. Which columns name a district and an attendance rate?
names(SCHOOL_INFO)
```

Check 2 matters more than it looks. The quiz writes `"ELA8"` and `"Math7"` and `"MATH4"` — mixed
case — so the real values have to come from `count()`, not from the question text. A filter against a
misspelled level returns zero rows and no error.

Check 3 is the one that silently costs a point. If `PERCENT_PROFICIENT` runs 0–100 you divide by
100; if it runs 0–1 you do not. Every command below is written assuming **0–100** — change
`/ 100` to nothing if the summary shows a maximum near 1.

---

### Q5 — How many NYS public school districts are represented?

```r
SCHOOL_INFO |> summarize(
    n_districts     = n_distinct(DISTRICT_NAME),
    n_districts_nona = n_distinct(DISTRICT_NAME, na.rm = TRUE),
    n_missing       = sum(is.na(DISTRICT_NAME))
)
```

Substitute whatever the district column is actually called from check 4.

**The trap:** `n_distinct()` counts `NA` as its own level by default. If `n_missing` is 0 the two
numbers agree and you are done; if not, report the `na.rm = TRUE` version, since a missing value is
not a district.

---

### Q6 — Schools in Long Island with a reported attendance rate below 90%

```r
# Confirm the scale first -- an attendance rate can be stored as 90 or as 0.90.
summary(SCHOOL_INFO$ATTENDANCE_RATE)

SCHOOL_INFO |>
    filter(REGION == "Long Island") |>
    summarize(
        schools_in_region = n(),
        missing_rate      = sum(is.na(ATTENDANCE_RATE)),
        below_90          = sum(ATTENDANCE_RATE < 90, na.rm = TRUE)
    )
```

**Two traps, one of which is your old friend.** `filter(ATTENDANCE_RATE < 90)` drops `NA` rows
silently — the same silent deletion that has bitten this course all semester. Here that is probably
the behaviour the question wants, since it says *"reported* attendance rate", but you should see the
`missing_rate` number before you accept it rather than after.

The second is the scale: if attendance is stored as a proportion, `< 90` is true for every school
and you would get the full region count back. If `below_90` equals `schools_in_region`, that is what
happened — switch to `< 0.90`.

---

### Q7 — Male students who took ELA8

```r
TEST_RESULTS |>
    filter(ASSESSMENT_NAME == "ELA8", SUBGROUP_NAME == "Male") |>
    summarize(n_rows = n(), students = sum(N_STUDENTS, na.rm = TRUE))
```

**The trap:** the question asks how many *students*, so the answer is `students`, not `n_rows`. Each
row is a school × test × subgroup combination, not a student. `n_rows` is printed alongside only so
you can see they are different and not confuse them.

---

### Q8 — Female students who took Math7 in NYC schools

This needs the join.

```r
# Use whatever intersect() reported as the shared key.
JOINED <- TEST_RESULTS |> inner_join(SCHOOL_INFO, by = join_by(<KEY_COLUMN>))

# Join sanity check -- run this before trusting anything built on JOINED.
c(test_rows   = nrow(TEST_RESULTS),
  joined_rows = nrow(JOINED),
  unmatched   = nrow(anti_join(TEST_RESULTS, SCHOOL_INFO, by = join_by(<KEY_COLUMN>))))

JOINED |>
    filter(ASSESSMENT_NAME == "Math7",
           SUBGROUP_NAME   == "Female",
           DISTRICT_TYPE   == "NYC") |>
    summarize(students = sum(N_STUDENTS, na.rm = TRUE))
```

**The trap, straight out of PA #05.** If `joined_rows` is *larger* than `test_rows`, the key is not
unique in `SCHOOL_INFO` and rows multiplied — every count built on `JOINED` is then inflated. If it
is *smaller*, `inner_join` dropped test results whose school has no covariate record, which may or
may not be acceptable. Either way you want to have looked. Adding `relationship = "one-to-one"` to
the `join_by()` turns the silent warning into a hard error, which is the safer default.

---

### Q9 — Standardized tests given, across All Students

```r
TEST_RESULTS |>
    filter(SUBGROUP_NAME == "All Students") |>
    summarize(tests_given = sum(N_STUDENTS, na.rm = TRUE))
```

The hint explains itself: every student appears in several subgroup rows (All Students, Male,
Economically Disadvantaged, and so on), so summing across all of them would count the same student
repeatedly. Restricting to `"All Students"` counts each once.

Confirm the exact spelling from check 2 — `"All Students"` with a capital S is the usual NYSED form,
but verify rather than assume.

---

### Q10 — Standardized tests passed

```r
TEST_RESULTS |>
    filter(SUBGROUP_NAME == "All Students") |>
    mutate(N_PROFICIENT = N_STUDENTS * PERCENT_PROFICIENT / 100) |>   # drop /100 if 0-1 scale
    summarize(
        rows_usable = sum(!is.na(PERCENT_PROFICIENT)),
        rows_missing = sum(is.na(PERCENT_PROFICIENT)),
        tests_passed = round(sum(N_PROFICIENT, na.rm = TRUE))
    )
```

**Three traps here.**

1. **The scale.** From check 3. Wrong by a factor of 100 if guessed.
2. **`SUBGROUP_NAME == "All Students"` still applies.** The hint is attached to Q9, but Q10 counts
   the same tests — if you drop the subgroup filter you double-count exactly as Q9 warned.
3. **Suppressed percentages.** NYSED masks results for small cells, so `PERCENT_PROFICIENT` will have
   `NA`s. `na.rm = TRUE` treats those schools as contributing zero passes, which understates the
   total. `rows_missing` tells you how badly. The professor has allowed a tolerance on this question,
   which is itself a hint that rounding and suppression make an exact match unlikely.

---

### Q11 and Q12 — Tests passed on Long Island

**These two questions are word-for-word identical** on your screenshot — same wording, same REGION,
same hint. That is almost certainly a Brightspace duplication error rather than a hidden distinction.

Compute it once and enter the same number in both. If you want to be careful, message the professor
or note it in the quiz comment box, but do not burn time hunting for a difference that is not there.

```r
JOINED |>
    filter(SUBGROUP_NAME == "All Students", REGION == "Long Island") |>
    mutate(N_PROFICIENT = N_STUDENTS * PERCENT_PROFICIENT / 100) |>
    summarize(tests_passed = round(sum(N_PROFICIENT, na.rm = TRUE)))
```

Same three traps as Q10, plus the join check from Q8.

---

### Q13 — Highest pass rate among NYC charter schools

```r
JOINED |>
    filter(SUBGROUP_NAME == "All Students",
           REGION        == "New York City",
           DISTRICT_TYPE == "Charter School") |>
    mutate(N_PROFICIENT = N_STUDENTS * PERCENT_PROFICIENT / 100) |>
    summarize(students  = sum(N_STUDENTS,   na.rm = TRUE),
              passed    = sum(N_PROFICIENT, na.rm = TRUE),
              n_schools = n(),
              .by = ASSESSMENT_NAME) |>
    mutate(pass_rate = passed / students) |>
    arrange(desc(pass_rate)) |>
    print(n = Inf)
```

**The trap that matters most on this question:** pass rate must be computed as
**`sum(passed) / sum(students)`**, not as `mean(PERCENT_PROFICIENT)`. The second weights a school with
12 test-takers the same as one with 400, and on a small subset like NYC charter schools the two
answers can easily rank the tests differently. Compute the ratio of sums.

Then match the top row against the five options — Combined8Math, MATH4, MATH3, RegentsMath8,
RecentsScience8. **The winner overall may not be one of the five**, so if the top row is some other
assessment, read down to the first option that appears in the list. (Note "RecentsScience8" in option
5 looks like a typo for "RegentsScience8"; match on what the data actually contains.)

`n_schools` is printed so you can discard any assessment resting on one or two schools.

---

### Q14 — Region with the most Economically Disadvantaged ELA8 test-takers

```r
JOINED |>
    filter(ASSESSMENT_NAME == "ELA8",
           SUBGROUP_NAME   == "Economically Disadvantaged") |>
    summarize(students = sum(N_STUDENTS, na.rm = TRUE), .by = REGION) |>
    arrange(desc(students)) |>
    print(n = Inf)
```

**Read the question carefully: it asks who *took* the test, not who passed.** So this is
`N_STUDENTS`, with no `PERCENT_PROFICIENT` anywhere in it.

The four options are Central New York, Capital District, Southern Tier and Mohawk Valley — all
upstate, which means **New York City and Long Island will almost certainly top the full list and are
not offered.** Print every region, then pick the highest-ranked one that appears among the four.
Taking the top row without looking is how this question is designed to be missed.

The question also warns that figures are suppressed for small counts and tells you to give the
answer the data provides. Take that at face value.

---

## Checklist before submitting

| | |
|---|---|
| Ran `glimpse()` on both files | |
| Confirmed the join key with `intersect()` | |
| Confirmed `PERCENT_PROFICIENT` scale (0–100 vs 0–1) | |
| Confirmed exact spelling of every filter value with `count()` | |
| Join row count checked — no multiplication, unmatched rows understood | |
| Q7, Q9, Q14 answered with `sum(N_STUDENTS)`, not `n()` | |
| Q9–Q13 filtered to `"All Students"` | |
| Q13 pass rate computed as sum ÷ sum, not mean of percentages | |
| Q14 answer chosen from the four offered options, not the overall top row | |
| Q11 and Q12 given the same number | |
| Q15 False · Q16 color · Q17 fill · Q18 color=NA · Q19 alpha | |

---

## Sources

- [ggplot2 `geom_path` / `geom_line` reference — aesthetics list](https://rdocumentation.org/packages/ggplot2/topics/geom_path)
- [ggplot2 aesthetic specifications](https://ggplot2.tidyverse.org/articles/ggplot2-specs.html)
- [ggplot2 `aes_linetype_size_shape` reference](https://ggplot2.tidyverse.org/reference/aes_linetype_size_shape.html)
