> **Coursework · STA 9750 · Lab 4 — Single-table verbs, NA, group-aware filtering** · Tag: 📚 **General Coursework** (with 🔁 transferable bits)
> Companion: [Lecture2-3_R-Quarto](./Lecture2-3_R-Quarto.md) · [Week5_base-R-grammar](./Week5_base-R-grammar.md) · index: [coursework-README](../_legacy/coursework-README-v1.md)
> *🔁 Transferable: R's NA/contagion model, `mean`-of-a-logical-is-a-rate, and "verify by
> relationship not appearance" all carry straight to pandas/SQL. Original journal below, verbatim.*

---

# STA 9750 Lab 4 — Single Table Verbs, NA, and Group-Aware Filtering

**Software Tools for Data Analysis · Baruch College · Prof. Michael Weylandt · Fall 2026**
Class #04 · Worked 1–2 October 2026 · Data: `flights` from `nycflights13` (336,776 × 19)

```r
if(!require("tidyverse")) install.packages("tidyverse")
if(!require("nycflights13")) install.packages("nycflights13")
library(tidyverse); library(nycflights13)
glimpse(flights)
```

---

## Part 1 — R's missing data model

### The puzzle that opens the lab

```r
flights |> filter(arr_delay == max(arr_delay))
# A tibble: 0 × 19
```

**Zero rows.** Something must be the maximum delay, so why nothing back?

Three rules compound, and each is reasonable alone:

1. `arr_delay` contains `NA`s, so `max(arr_delay)` is `NA` — **missingness is contagious**
2. `arr_delay == NA` is `NA` for every row — you can't compare a known value to an unknown one
3. **`filter` keeps rows that are `TRUE`, not rows that aren't `FALSE`** — so every `NA` is dropped

No error. No warning. An empty table.

The control shows the comparison itself is fine:

```r
flights |> filter(arr_delay == 60)      # 528 rows
flights |> summarize(max(arr_delay))    # NA  ← the culprit
```

### `NA` versus `NaN`

`NA` is **statistical** missingness — the value exists and is well defined, we just don't know it.
`NaN` is **invalid arithmetic**.

```r
0 / 0      # NaN  — invalid
3 + NA     # NA   — unknown
NA > 0     # NA
NA / 4     # NA
0 * NA     # NA   ← surprising
0 * Inf    # NaN  ← why
```

`0 * NA` is `NA` because the hidden value could be `Inf`, and `0 * Inf` is `NaN`. Since the result
could be `0` or `NaN`, it stays unknown.

**`NA` is occasionally over-ruled:**

```r
any(c(NA, TRUE))    # TRUE
any(c(TRUE, TRUE))  # TRUE
any(c(FALSE, TRUE)) # TRUE
```

Both possible values of the `NA` give `TRUE`, so the unknown doesn't matter.

### Not all `NA`s are the same `NA`

```r
NA == NA   # NA, not TRUE

today_temp    <- NA
tomorrow_temp <- NA
today_temp == tomorrow_temp   # NA
today_temp  - tomorrow_temp   # NA
```

Is today the same temperature as tomorrow? **If you don't know either one, you can't say.** That
intuition is the whole design.

### Dealing with it: `na.rm` and `is.na`

```r
flights |> summarize(max(arr_delay, na.rm = TRUE))     # 1272
flights |> filter(arr_delay == max(arr_delay, na.rm = TRUE))
```

The most delayed flight in the data:

| | |
|---|---|
| Carrier / flight | **HA 51** |
| Route | JFK → HNL (4,983 mi) |
| `dep_delay` | **1,301 min** |
| `arr_delay` | **1,272 min** |

It left 1,301 minutes late and arrived 1,272 late — **it made up 29 minutes over the Pacific.**
That detail is the answer to the "make up time in flight" question hiding in plain sight.

`na.rm` isn't available everywhere, so the general tool is `is.na`:

```r
x <- c(1, 2, 3, NA, 5)
is.na(x)                              # FALSE FALSE FALSE TRUE FALSE
flights |> filter(!is.na(arr_delay))  # 327,346 rows
```

> **`drop_na()`** removes any row with an `NA` in *any* column. Blunt — do you really need to drop
> a row when computing `X` because `Y` is missing? Fine for quick work, not without looking first.

### `filter` discards `NA` silently

```r
tiny_example <- tribble(~letter, ~value, "a", 1, "b", NA)
tiny_example |> filter(value > 0)
# only row "a" survives
```

Row `b` isn't kept and isn't reported. **Most `NA` rows vanish the moment you start filtering.**

### How much is missing, and what kind

```r
flights |> filter(!is.na(arr_delay))                       # 327,346
flights |> filter(is.na(arr_delay))            |> NROW()   #   9,430
flights |> filter(is.na(arr_delay), !is.na(arr_time)) |> NROW()   #     717
```

| | Count |
|---|---|
| Total | 336,776 |
| Has `arr_delay` | 327,346 |
| Missing `arr_delay` | 9,430 |
| **Missing delay but *has* an arrival time** | **717** |

Those 717 landed — the delay simply wasn't recorded. The other ~8,700 are cancellations.

**And that forces a judgement call.** Is a cancelled flight infinitely delayed? 24 hours delayed,
if passengers were rebooked the next day? Or not a delay at all?

> Weylandt's answer: there isn't a right one. If you're the DOT worrying about passengers, a
> cancellation is *very* delayed. If you're a Boeing engineer studying flight speeds, it's
> useless data. His prescription is **reproducible transparency** — use Quarto, show the code,
> document the choice, and let a subject-matter expert disagree with something visible.

---

## Part 2 — Booleans and `filter`

```r
filter(flights, month == 1, day == 1)       # 842 rows
jan1 <- filter(flights, month == 1, day == 1)
```

`filter` returns a **new** data frame; it never modifies the original. Save it with `<-` or it's gone.

Multiple tests are combined with an implied **and**.

### Operators

`>` `>=` `<` `<=` `!=` `==` for comparison; `&` (and), `|` (or), `!` (not), `xor()` for combining.
`|` is true if either is true; `xor()` is true if exactly one is.

### The order-of-operations trap

```r
filter(flights, month == 11 | 12)    # WRONG
```

English lets you say "November or December." R does not. **Each side of a Boolean operator needs a
complete test.** The shorthand:

```r
nov_dec <- filter(flights, month %in% c(11, 12))
```

### De Morgan's laws, checked rather than trusted

`!(x & y)` is `!x | !y`, and `!(x | y)` is `!x & !y`.

```r
dml1 <- filter(flights, !(arr_delay > 120 | dep_delay > 120))
dml2 <- filter(flights,   arr_delay <= 120, dep_delay <= 120)
identical(dml1, dml2)   # TRUE   ← 316,050 rows each
```

### `=` versus `==`

```r
filter(flights, month = 1)
# Error in `filter()`:
# ! We detected a named input.
# ℹ This usually means that you've used `=` instead of `==`.
# ℹ Did you mean `month == 1`?
```

A rare case of an error message that diagnoses itself. Worth recognising on sight.

**Also:** `&&` and `||` exist but should *not* be used inside `filter()`.

---

## Part 3 — Grouped operations

The motivating question: **which carriers have flights later than average?**

```r
flights |> filter(!is.na(arr_delay)) |> summarize(mean(arr_delay))   # 6.90
```

### Three ways, each better than the last

**1 — hardcode the number.** Works, goes stale the moment the data changes.

```r
flights |> filter(!is.na(arr_delay)) |> filter(arr_delay > 6.90) |>
  group_by(carrier) |> summarize(n = n())
```

**2 — compute it into a variable.** Better, but filters for `NA` twice.

```r
avg_delay <- flights |> filter(!is.na(arr_delay)) |>
  summarize(mean_delay = mean(arr_delay)) |> pull(mean_delay)
```

> Both produced **identical counts** — which is how you confirm a refactor didn't change behaviour.

**3 — `mutate` the mean into a column.** One pass, nothing repeated.

```r
flights |>
  filter(!is.na(arr_delay)) |>
  mutate(mean_delay = mean(arr_delay)) |>
  select(mean_delay, arr_delay, carrier, everything()) |>
  filter(arr_delay > mean_delay) |>
  group_by(carrier) |> summarize(n = n())
```

The `mean_delay` column is just `6.90` repeated 327,346 times — **R's recycling rule** filling the
column. `everything()` inside `select()` is the trick for reordering columns without listing them all.

### Where `group_by` sits changes the question

Move `group_by(carrier)` to the **front** and `mean_delay` becomes each carrier's own average:

| Carrier | vs global 6.90 | vs own mean | own mean |
|---|---|---|---|
| **AA** | 8,399 | **10,706** ↑ | 0.364 |
| **DL** | 12,549 | **15,698** ↑ | 1.64 |
| **UA** | 17,551 | **19,722** ↑ | 3.56 |
| **B6** | 18,955 | **17,101** ↓ | 9.46 |
| **EV** | 20,354 | **16,028** ↓ | 15.8 |

**The rule:** a carrier whose own average is *below* 6.90 gets *more* flights flagged; one above
gets *fewer*.

AA's bar is 0.364 minutes, so nearly any delay counts. EV's is 15.8, so a ten-minute delay is
"normal for EV." **You're holding the punctual airlines to their own high standard and letting the
late ones off against theirs.** Neither version is wrong — they're different questions.

### `.by` leaves no residue

```r
# group_by version — output carries:  # Groups: carrier [16]
# .by version      — no Groups line
flights |> filter(!is.na(arr_delay)) |>
  mutate(mean_delay = mean(arr_delay), .by = carrier) |>
  select(mean_delay, arr_delay, carrier, everything())
```

Identical values. But `group_by` **stays attached**, and `summarize` removes only one layer — so a
later verb can silently operate group-wise. `.by` applies to one command and leaves nothing behind.

### The `HAVING` pattern — group-level filtering

Average delay *of large airlines*, defined as >10,000 departures. Two routes:

```r
# A — summarize, then filter. Loses all flight-level detail.
flights |> filter(!is.na(arr_delay)) |> group_by(carrier) |>
  summarize(n = n(), mean_delay = mean(arr_delay)) |> filter(n > 10000)

# B — count group-wise, filter, then summarize. Keeps the rows.
flights |> filter(!is.na(arr_delay)) |> group_by(carrier) |>
  mutate(n = n()) |> filter(n > 10000) |> summarize(mean_delay = mean(arr_delay))
```

Both give the same nine carriers: `9E AA B6 DL EV MQ UA US WN`.

**Route B adapts to non-summarizing questions**, which A can't:

```r
flights |> filter(!is.na(arr_delay)) |> group_by(carrier) |>
  mutate(n = n()) |>
  filter(n > 10000, arr_delay > 0, dest %in% c("HOU", "IAH")) |>
  summarize(n = n())
#  AA 159 · B6 310 · UA 2724 · WN 591
```

> Note `n` gets re-used and quietly overwritten. Acceptable for a throwaway name; avoid it for
> real data columns.

---

## Part 4 — The eight questions

### The pattern behind all of them

Every one of these is the same move — translating a business-style question into a grouped
operation:

```
Natural-language question
        ↓
Define a logical condition          dep_delay > 0
        ↓
Group observations                  group_by(carrier)
        ↓
Summarize counts / rates            mean(condition, na.rm = TRUE)
        ↓
Rank or filter the result           slice_min() / slice_max()
```

In code:

```r
flights |>
  group_by(group_variable) |>
  summarize(rate = mean(condition, na.rm = TRUE))
```

**Why `mean` of a condition is a rate:** `TRUE` behaves as `1` and `FALSE` as `0`, so
`mean(dep_delay > 0, na.rm = TRUE)` is literally the fraction of flights that left late. That one
idea answers five of the eight questions below.

**Counts and rates answer different questions.** A carrier with many delayed flights may simply
operate many flights. Raw counts measure volume; group-specific proportions measure performance.
Choosing the wrong one produces a true number that misleads.

**`na.rm = TRUE` is not optional here.** Without it, `mean()` and `max()` return `NA` — exactly what
happened at the top of this lab with `max(arr_delay)`.

### 1 · Lowest rate of delayed flights

```r
flights |> group_by(carrier) |>
  summarize(delay_rate = mean(dep_delay > 0, na.rm = TRUE)) |>
  slice_min(delay_rate)
# HA  0.202
```

**⚠ Sample size.** `HA` is not among the nine carriers with >10,000 flights. Hawaiian flies one
route out of JFK. The arithmetic is right; the finding is about a few hundred flights.

```r
# fix — report n, or restrict to carriers with volume
flights |> group_by(carrier) |>
  summarize(n = n(), delay_rate = mean(dep_delay > 0, na.rm = TRUE)) |>
  filter(n > 10000) |> slice_min(delay_rate)
```

**Also a definition:** `> 0` counts one minute late as delayed. The DOT standard is 15 minutes.
Either is defensible; state which.

### 2 · Highest chance of early arrivals

```r
flights |> group_by(carrier) |>
  summarize(early_rate = mean(arr_delay < 0, na.rm = TRUE)) |>
  slice_max(early_rate)
# AS  0.722
```

Same caveat — `AS` is also below 10,000 flights.

### 3 · Most likely to make up time in flight

```r
flights |> filter(dep_delay > 0) |> group_by(carrier) |>
  summarize(makeup_rate = mean(arr_delay < dep_delay, na.rm = TRUE)) |>
  slice_max(makeup_rate)
# AS  0.782
```

A well-posed definition: among flights that left late, how often did arrival delay come in under
departure delay. But it counts **any** improvement — one minute scores like forty.

```r
mean(arr_delay < dep_delay, na.rm = TRUE)   # how OFTEN
mean(dep_delay - arr_delay, na.rm = TRUE)   # how MUCH
```

### 4 · Origin airport with the highest delay rate

```r
flights |> group_by(origin) |>
  summarize(delay_rate = mean(dep_delay > 0, na.rm = TRUE)) |>
  slice_max(delay_rate)
# EWR  0.448
```

**No caveat needed** — three origins, all enormous. This one is solid as written.

### 5 · Month with the most flights

```r
flights |> count(month) |> slice_max(n)
# July (7)  29,425
```

`count()` includes cancelled flights. For flights that actually flew, add `filter(!is.na(arr_time))`.

### 6 · Furthest flight

```r
flights |> slice_max(distance, n = 1, with_ties = FALSE) |>
  select(carrier, flight, origin, dest, distance)
# HA 51  JFK → HNL  4,983
```

**`with_ties = FALSE` hid a tie** — every HA flight on that route is 4,983 miles. The question is
ambiguous: furthest *route*, or one arbitrary row from it? For the route:

```r
flights |> distinct(origin, dest, distance) |> slice_max(distance)
```

### 7 · Shortest flight

```r
# US 1632  EWR → LGA  17 miles
```

A real curiosity in this dataset — 17 miles between two New York airports.

### 8 · Are longer flights more likely to be delayed?

The only question here that's a *relationship* rather than a lookup, and the answer is better than
yes or no.

```r
flights |>
  filter(!is.na(arr_delay)) |>
  mutate(dist_bin = cut(distance, breaks = c(0, 500, 1000, 1500, 2000, 3000, 5000))) |>
  group_by(dist_bin) |>
  summarize(n = n(),
            dep_rate = mean(dep_delay > 0, na.rm = TRUE),
            arr_rate = mean(arr_delay > 0),
            made_up  = mean(dep_delay - arr_delay, na.rm = TRUE))
```

| Distance | n | dep_rate | arr_rate | arr − dep | made up |
|---|---|---|---|---|---|
| 0–500 | 76,775 | 0.369 | 0.405 | **+0.036** | 4.05 |
| 500–1,000 | 105,819 | 0.387 | 0.427 | **+0.040** | 4.42 |
| 1,000–1,500 | 72,798 | 0.386 | 0.402 | **+0.016** | 6.06 |
| 1,500–2,000 | 20,772 | 0.440 | 0.408 | **−0.032** | 7.18 |
| 2,000–3,000 | 50,473 | 0.415 | 0.373 | **−0.042** | 9.44 |
| 3,000–5,000 | 709 | 0.403 | 0.344 | **−0.059** | 10.7 |

**Departure delay barely moves** — 0.369 to 0.403, wobbling. The aircraft hasn't left yet; it
doesn't know how far it's going. Getting a null result here is reassuring, not boring.

**Arrival delay falls** — 0.427 at the peak down to 0.344.

**`made_up` is monotonic across all six bins** — 4.05 → 10.7, no exceptions. That's the mechanism:
more air time, more room to absorb a late departure.

**The sign flips around 1,500 miles.** Below it, more flights arrive late than departed late — they
*lose* time. Above it, fewer do — they *gain* it.

> **Answer:** longer flights are *slightly more* likely to leave late and *meaningfully less* likely
> to land late, because past roughly 1,500 miles the cruise is long enough to absorb the delay.

**Caveats to state:** the top bin holds only 709 flights, so lean on bins 1–5. And the 1,500–2,000
bin has the highest departure-delay rate of all (0.440), which distance doesn't explain — probably
route mix.

---

## Part 5 — Gotchas met along the way

### `.by` takes column names, not expressions

```r
summarize(..., .by = cut(distance, breaks = c(...)))
# Error: object 'distance' not found
```

`.by` uses **tidyselect** — it accepts column selections, not computed expressions. Build the
column with `mutate` first, then group by its name.

Convenient, but narrower than `group_by`.

### `filter` drops `NA` rows without saying so

Covered above, and worth repeating because it's the single most common way a dplyr pipeline
silently returns the wrong number of rows.

### `group_by` persists; `summarize` peels one layer

A pipeline that groups, mutates and filters is *still grouped* at the end. Use `.by` for one-shot
grouping, or `ungroup()` when you're done.

### Contributing factors — not in this dataset, but the same idea

Officer- or operator-entered categorical fields tend to have a dominant default value. Treat them
as weak evidence rather than building a finding on them.

---

## Part 6 — What to carry forward

**`sum` of a logical counts; `mean` of a logical is a rate.** `mean(dep_delay > 0)` is a delay
rate, and that one idea answers five of the eight questions.

**`filter` keeps `TRUE`, not "not `FALSE`."** Everything about `NA` in dplyr follows from this.

**Verify by relationship, not appearance.** `identical(dml1, dml2)` proving De Morgan's law;
`made_up` being monotonic across six independent bins; the refactored `avg_delay` returning
identical counts. Each is a check that a single number couldn't give you.

**Rates need denominators.** Three of the eight answers landed on carriers the lab's own `HAVING`
example had already filtered out. The method was right and the reporting was incomplete.

**State your definitions.** "Delayed" meaning `> 0` versus `>= 15`; departure versus arrival delay;
"made up time" as frequency versus magnitude. None is wrong, all change the answer.

**Where a verb sits is part of the question.** `group_by` before versus after `mutate` produced
8,399 against 10,706 for the same airline. Same verbs, same data, one line moved.

**Counts measure volume; rates measure performance.** EV has the most flights above the global
average delay — and also one of the largest fleets. The raw count was never the answer.

---

> **The hard part was not the syntax.** `group_by |> summarize |> slice_max` is three lines and
> took minutes to learn. Deciding what "delayed" means, whether to measure departure or arrival,
> whether "makes up time" means *how often* or *how much*, and whether a 342-flight carrier
> belongs in the ranking at all — that was the work. This lab is where the course stops being
> about data manipulation and starts being about analytical reasoning.

---

## Still open

The **seven filter exercises** from the lab's own Exercises section (pages 9–10) — not yet
attempted:

1. Arrival delay of two or more hours
2. Flew to Houston (`IAH` or `HOU`)
3. Operated by United (`UA`), American (`AA`), or Delta (`DL`)
4. Departed in summer (June, July, August)
5. Arrived more than two hours late, but didn't leave late
6. Delayed more than an hour, but made up more than 30 minutes in flight
7. Departed between midnight and 6am inclusive

Numbers 5 and 7 have traps in them.

---

*Readings follow R for Data Science §5.2. Filter exercises adapted from the `learnr` documentation.*
