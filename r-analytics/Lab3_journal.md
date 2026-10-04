# Lab 3 Journal — R, These Are Your First Steps

**STA 9750 · Thursday section · worked Thu 17 Sep 2026, evening**
Activity: <https://michael-weylandt.com/STA9750/labs/lab03.html>
R 4.6.1 "Happy Hop" · aarch64-apple-darwin23 · macOS Tahoe 26.5.1

Status: **Parts 1–6 ✓ · all five exercises ✓**

Worked entirely by typing and predicting rather than reading solutions. Four things turned up
that aren't in the handout, including a bug in one of his own solutions.

---

## Corrections and findings

### 1. `? %%` doesn't work

The page says to type `? %%` for help on the modulo operator. `?` can't parse a bare operator and
it errors. Use `help("%%")` or ``?`%%` ``.

### 2. His Exercise 5 solution has the Exercise 3 bug

`hero_sqrt` uses `for(i in 1:iter)`. So:

```r
hero_sqrt(3, iter=0)   # 1.732143 — two full iterations
```

It should return the starting guess, `1.5`. `1:0` is `c(1, 0)`, length 2. `seq_len(iter)` fixes it.

Exercise 3 is where the handout implicitly needs `seq_len` (`greetings(times=0)`), and Exercise 5
is where he ships the bug himself. Never warned about on the page.

### 3. `all.equal` never returns `FALSE`

```r
all.equal(1, 2)              # "Mean relative difference: 1"  ← a STRING
isTRUE(all.equal(1, 2))      # FALSE
if(all.equal(1, 2)){ }       # Error: argument is not interpretable as logical
```

Equal within tolerance → `TRUE`. Not equal → a *character description* of the difference. So a bare
`all.equal` inside `if` can only pass or crash — it can never fail cleanly.

**`all.equal` is a diagnostic. `isTRUE(all.equal(...))` is a test.** His spec only works because
it passes.

### 4. `-4/0` is `-Inf`, not `NaN`

```r
hero_sqrt(0)     # NaN   — hits 0/0
hero_sqrt(-4)    # -Inf  — passes through 0, then -4/0
```

`0/0` is undefined → `NaN`. Anything-else `/0` is a defined limit → `±Inf`. Two different failure
modes from the same missing domain guard.

Follow-on: `is.na(NaN)` is `TRUE`, `is.na(Inf)` is `FALSE`. So `na.rm=TRUE` silently drops `NaN`
and keeps `Inf`, which then poisons whatever you were averaging.

### 5. A name collision that lies about itself

```r
sum(1/(1:terms)^k)
# Error in 1:terms : NA/NaN argument
```

Not `object 'terms' not found`. Because **`terms` is a function in `stats`**, attached in every
session — it was the first entry in my very first `sessionInfo()`. R found it, couldn't turn a
function into a number, and reported a numeric problem instead of a missing one.

`c`, `t`, `df`, `data`, `mean`, `sum`, `max`, `length` are all taken too. **When an error message
describes a different problem than the one you have, type the bare name and see what's actually
there.**

---

## The three silences

Three separate times tonight, code ran cleanly and did the wrong thing. No error, no warning.

| What happened | Why | Where it'll recur |
|---|---|---|
| `prices * discount` recycled `c(0.9, 0.8)` across 6 prices | lengths divided evenly, so no warning | any elementwise op on mismatched columns |
| `greetings(times=0)` greeted twice | `1:0` is `c(1, 0)` | any loop over a possibly-empty result |
| `greetings("Sam", TRUE)` dropped the emphasis | `TRUE` landed on `times`, coerced to 1 | any positional call past the first argument |

**R warns about *ragged* recycling, not *wrong* recycling.** Silence means the arithmetic was
tidy, never that I got what I meant. Same shape as December vanishing off the Lab 2 chart.

The habit that catches all three: predict the output before pressing Enter, and investigate every
gap between the prediction and the result.

---

## Return values — the table I kept needing

| Body ends with | Returns | Printed |
|---|---|---|
| `{}` empty | `NULL` | **`NULL`**, visible |
| `z <- x + y` | the value | nothing — assignment is invisible |
| `for(...){ }` | `NULL` | nothing — invisible `NULL` |
| `cat(...)` | `NULL` | the side effect only |
| bare `x` | `x` | `x` |

Only the last does what I usually want. Hit three of the five by accident.

**The spec tells you which one to write.** Exercise 1's expected output has no `[1]`, so it's a
`cat` function. Exercise 2's has `[1]`, so it returns a value. Read the brackets.

---

## Scope: loops leak, functions don't

Produced both halves by accident:

```r
i           # 20  — still there after the loop finished
emphasis    # Error: object 'emphasis' not found
```

| | Creates a scope? | Afterwards |
|---|---|---|
| `for(i in ...)` | **no** | `i` persists in the global environment |
| `function(emphasis)` | **yes** | gone the moment the call returns |

A function can't silently pick up a leftover variable and can't leave debris behind. A loop does
both. That's the argument for **Session → Restart R** whenever something stops making sense.

---

## `cat` flattens; `paste` vectorizes

```r
cat(1:3, "\n")       # identical output
cat(1, 2, 3, "\n")   # identical output

paste(1:3, c("a","b","c"))   # "1 a" "2 b" "3 c"  — three results
paste(1, 2, 3)               # "1 2 3"            — one result
```

`cat` can't tell three arguments from one argument of length three. It dumps everything into a
single flat stream. `paste` keeps its arguments as parallel vectors and recycles, like `+`.

Which explains why some of my functions vectorized for free and one didn't:

- `my_addition` — built on `+` → vectorizes
- `leap_year` — built on `%%`, `&`, `|` → vectorizes, handled 400 years unchanged
- `greetings` — built on `cat` and `for` → `c("Sam","Alex")` gave `Hello, SamAlex`

**A function inherits the behaviour of whatever it's built from.** You never choose vectorization
directly; you choose it by choosing your primitives.

Corollary from Part 4: a function built on `if` can't go inside `mutate()`. Route A of `leap_year`
(`&` / `|`) handles a column; Route B (`if` / `return`) errors with *the condition has length > 1*.

---

## `stopifnot` checks type, not length

```r
greetings(TRUE)                      # Error — caught
greetings(name=c("Sam","Alex"))      # passes! → "Hello, SamAlex"
```

`is.character(c("Sam","Alex"))` is `TRUE` — every type predicate returns one value regardless of
input length. Add `length(name) == 1` explicitly if the function assumes a scalar.

Nice property: `stopifnot` uses the **source text of the failing condition** as the error message,
so `is.character(name) is not TRUE` writes itself.

---

## Numerical lessons — the pairing of Exercises 4 and 5

### `zeta`: error is exactly 1/n

| terms | `zeta(2)` | error | 1/n |
|---|---|---|---|
| 10 | 1.549768 | 0.0951663 | 0.1 |
| 100 | 1.634984 | 0.0099502 | 0.01 |
| 1,000 | 1.643935 | 0.0009995 | 0.001 |
| 10,000 | 1.644834 | 0.0001000 | 0.0001 |
| 500,000 | 1.644932 | 0.0000020 | 0.000002 |

Everything omitted is the tail, and the tail collapses to `1/n`. **Tenfold more work buys one
digit.** Fifteen digits would need ~10¹⁵ terms — about 80 GB of memory in one line. The method
doesn't scale.

Always *below*, never above, because every omitted term is positive. That one-sidedness is how you
tell truncation error from floating-point error.

### `hero_sqrt`: error squares each step

```
iter 1: error 1.79e-02
iter 2: error 9.20e-05
iter 3: error 2.45e-09
iter 4: error 0
```

Each error is the square of the last, times a constant:

```r
9.2e-5 / (1.79e-2)^2     # 0.287
2.45e-9 / (9.2e-5)^2     # 0.289
1 / (2*sqrt(3))          # 0.2887
```

That's Newton's method: `e_next ≈ e² / (2√n)`. Three numbers off the screen and the theorem falls
out of them.

### The comparison is the point

| | `zeta` | `hero_sqrt` |
|---|---|---|
| Vectorizable | yes | **no** — each step needs the last |
| Error per step | ÷10 for 10× the work | **squared** |
| Cost of 15 digits | ~10¹⁵ terms, impossible | 4 iterations |

The vectorizable one is hopeless; the loop-bound one finishes before you've read the output.
**Choosing a better method beats optimising a worse one.** Exercise 4 before 5 makes that point
without saying it.

### And the finale

```r
hero_sqrt(3) == sqrt(3)         # TRUE   — bit-perfect
hero_sqrt(3)^2 == 3             # FALSE
print(hero_sqrt(3)^2, digits=20) # 2.9999999999999995559
all.equal(hero_sqrt(3)^2, 3)    # TRUE
```

**The method was exact. The check broke it.** The obvious verification — square it and see if you
get 3 — is less reliable than the thing it's testing.

`3 - hero_sqrt(3)^2` is `4.44e-16`. There is no double between that number and 3.

**Rule: `==` on integers, counts and strings. `isTRUE(all.equal(...))` on anything that came out
of arithmetic.**

---

## Part 6 — the session model, run as an experiment

Worth three minutes, not a read.

1. Console: `my_secret <- 42`
2. A `.qmd` whose only chunk is `my_secret`
3. Green ▶ on the chunk → **42**
4. **Render** → `Error: object 'my_secret' not found`
5. Move `my_secret <- 42` inside the chunk → renders

| | Green ▶ | Render |
|---|---|---|
| Session | your Console | brand new, every time |
| Sees Console variables | yes | **no** |
| Output appears in | Console | **Background Jobs** |
| Passing means | the code runs | the code runs **and the document is complete** |

The Background Jobs tab *is* the second session, made visible. The command it ran was
`quarto preview ... --to html`, shelling out to the Quarto program — Render is a wrapper around a
command-line call, not an R function.

The error was unusually informative: `Quitting from lab3_part6.qmd:6-8 [unnamed-chunk-1]` — file,
line range, and chunk name. Naming chunks (`` ```{r load-data} ``) makes render failures
self-locating.

**Two things cross session boundaries**, because they're files on disk:

- **Installed packages** — `install.packages()` once, ever. Console only; a chunk containing it
  re-downloads on every render.
- **Saved data files** — a CSV written by one session reads fine in another.

Which resolves the Lab 2 puzzle: `tidyverse` had been installed for weeks, but `library(tidyverse)`
still had to be in a chunk. **Installing crosses. Attaching does not.**

> **Console is the workshop. The `.qmd` is the record.**

Iterate in the Console, paste the finished version into a chunk, **Render often**. One render at
the end of a three-hour session surfaces a dozen missing `library()` calls at once and gives no
clue which broke first.

Also noticed on the successful render: the code was visible on the page. `echo` defaults to `true` —
the other side of Lab 2's `#| echo: false`.

---

## Small things worth keeping

- **`} else {` always.** At the Console, `else` on its own line errors, because the `if` already
  completed and ran. Inside a function or chunk it's fine — R parses the whole block first.
- **A failed paste keeps executing.** When a multi-line paste dies partway, the remaining lines
  still get sent. Scroll up and check what ran.
- `sum()` and `mean()` on logicals → count and proportion. `mean(leap_year(2001:2400))` is
  `0.2425`, so the Gregorian year averages **365.2425 days**. The whole three-rule structure exists
  to hit that number.
- `cat` puts its separator between **every** argument, including before `"\n"` — hence trailing
  spaces, and hence `sep=""` in Exercise 1.
- `1:5^2` is `1:25`. `^` binds tighter than `:`. Same family as `1:5 + 1`.
- Printing a function without parentheses shows its source. `sd` reveals a default argument, a
  brace-free single-expression body, an `if` used as a value, and `<environment: namespace:stats>`.
- `%g` in `sprintf` switches to scientific above six significant digits — `1.23457e+06`. Wrong for
  a sentence about counts.
- R keeps **your** source text for functions you define, but reconstructs it for base functions.
  That's why `sd` printed reformatted and why an error said `year%%400` when I typed `year %% 400`.

---

## What carries into MP#00 and MP#01

- Writing functions with defaults; required arguments first
- `seq_len()` over `1:n`
- `isTRUE(all.equal(...))` over `==` for computed numbers
- `cat(..., sep="")` for readable output; named chunks for locatable errors
- Coercion — why a numeric column arrives as `chr`
- Recycling — why a result is silently wrong rather than loudly broken
- **The session model** — why Render fails when the chunk succeeded

Render every fifteen minutes. That's the one habit that prevents the MP#01 failure mode.

---

## Open items

- [ ] **BUS 9430 individual project plan — Sun 20 Sep, 5:00pm** ← nearest
- [ ] STA 9750 PA #04 — Thu 24 Sep, 6:00pm
- [ ] Team roster — Thu 24 Sep
- [ ] MP#00 — Fri 25 Sep, target Wed 23rd
- [ ] Project proposal slides + team contract — Thu 1 Oct, 6:00pm
- [ ] Team: check whether the LPI layers carry installation dates — decides whether SQ2 is possible
