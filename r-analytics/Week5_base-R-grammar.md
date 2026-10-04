> **Coursework · STA 9750 · Base R Grammar (reference + 12 practice questions)** · Tag: 📚 **General Coursework** (with 🔁 transferable bits)
> Companion: [Lab4_journal](./Lab4_journal.md) · index: [coursework-README](../coursework-README.md)
> *🔁 Transferable: the "quiet failures" catalogue (coercion, recycling, float equality, `seq_len`
> empty-range guard) is language-universal. Original reference note below, verbatim.*

---

# STA 9750 — Base R Grammar

**Extra R lecture, 1 October 2026 · Prof. Michael Weylandt**
Reference note plus the twelve practice questions. Every claim below was run in R 4.6.1 before it
was written down.

`dplyr` is a layer on top of this. When a pipeline behaves strangely, the explanation is almost
always somewhere in Part 1.

---

# Part 1 — The grammar

## 1.1 Scalars and types

| Type | Examples | Notes |
|---|---|---|
| **numeric** (double) | `10`, `3.14`, `0.0002`, `1.234e5` | the default for any bare number |
| **integer** | `1L`, `1:5` | note the `L`; `1:5` is integer, `c(1,2,3)` is not |
| **complex** | `3 + 4i` | rarely needed |
| **character** | `"Baruch"`, `'Michael'` | matched quotes, either kind |
| **logical** | `TRUE`, `FALSE` | no quotes, and `T`/`F` are shortcuts worth avoiding |

```r
class(3)            # "numeric"
class(3L)           # "integer"
class(1:5)          # "integer"   ← the colon makes integers
class("I love R!")  # "character"
class(TRUE)         # "logical"
```

Nest quotes by mixing them:

```r
"He said to me: 'Code is great!'"
```

> `class(1)` is `"numeric"` but `class(1L)` is `"integer"`. You rarely need the distinction — but
> `identical(1L, 1)` is **`FALSE`**, which will confuse you exactly once.

## 1.2 Logicals are numbers in disguise

```r
TRUE + TRUE                  # 2
sum(c(TRUE, FALSE, TRUE))    # 2    ← a count
mean(c(TRUE, FALSE, TRUE))   # 0.667 ← a proportion
```

**`sum` of a logical counts; `mean` of a logical is a rate.** This underlies most of Lab 4 and
several questions below.

## 1.3 Assignment and naming

```r
x <- 3
x^2            # 9

five_factorial <- 5 * 4 * 3 * 2 * 1   # right side evaluates first
six_factorial  <- 6 * five_factorial  # 720
```

**Assignment is the *last* operation**, so you can build results in stages.

| Rule | Good | Bad |
|---|---|---|
| One word, no spaces | `average_score_2026` | `Average Score 2026` |
| Letters, digits, underscores | `score_2026` | `score-2026` |
| Must start with a letter | `x2026` | `2026_score` |

Avoid reserved words: `if`, `else`, `for`, `function`, `TRUE`, `FALSE`, `NULL`, `NA`, `Inf`.

## 1.4 Dates are their own class

```r
today <- Sys.Date()   # "2026-10-01"
class(today)          # "Date"
today + 40            # "2026-11-10"
```

Date arithmetic works in days and knows month lengths. A string can't do that.

## 1.5 Vectors

An **ordered collection of one type**. Built with `c()` ("concatenate"):

```r
x <- c(1, 2, 3)
class(x)     # "numeric"
length(x)    # 3
length(3)    # 1   ← scalars ARE vectors of length one
```

### Coercion: one type wins

If you mix types, R silently promotes everything to the most general:

```
logical  →  integer  →  double  →  character
```

```r
class(c(TRUE, 1L))   # "integer"
class(c(1L, 2.5))    # "numeric"
c(1, "a")            # "1"    "a"      ← the number became text
c(TRUE, "a")         # "TRUE" "a"
```

**This happens without a warning.** A single stray string in a column of numbers turns the whole
column into text, and arithmetic on it then fails for reasons that look unrelated.

### Recycling

Shorter vectors repeat to match longer ones:

```r
c(1,2,3,4) + c(10,20)    # 11 22 13 24     ← silent, lengths divide evenly
c(1,2,3)   + c(10,20)    # 11 22 13  + WARNING
#   "longer object length is not a multiple of shorter object length"
```

**R warns only when the lengths don't divide evenly.** When they do, it's completely silent — which
is how a recycling bug survives. Question 5 below uses recycling deliberately.

## 1.6 Indexing with `[ ]`

```r
x <- c(a=10, b=20, c=30, d=40)
```

| Form | Result | |
|---|---|---|
| `x[2]` | `20` | by position |
| `x["c"]` | `30` | by name |
| `x[c(2,1,3)]` | reorder | positions in any order |
| `x[c(2,1,2,1)]` | repeat | elements can repeat freely |
| `x[-2]` | `10 30 40` | **negative DROPS** |
| `x[x > 15]` | `20 30 40` | logical mask |
| `x[c(TRUE,FALSE)]` | `10 30` | mask recycles |

**Negative indices drop; they do not count backwards.** `x[-2]` removes the second element. In
Python it would return the second-from-last. This is the most common cross-language mistake.

### Three indexing traps

```r
x[9]          # NA   ← out of bounds gives NA, not an error
x[0]          # empty vector, not an error
x[c(-1, 2)]   # ERROR: only 0's may be mixed with negative subscripts
```

And the subtle one:

```r
y <- c(1, NA, 3)
y[y > 1]          # NA 3     ← the NA condition produces an NA element
y[which(y > 1)]   # 3        ← which() drops it
```

A logical mask containing `NA` yields an `NA` in the output. `which()` converts to positions and
discards unknowns. **This is the base-R original of the `filter` behaviour from Lab 4.**

## 1.7 `NA`, `NaN`, `Inf`, `NULL`

| | Means | `length()` |
|---|---|---|
| `NA` | **unknown** value — exists, not recorded | 1 |
| `NaN` | **invalid** arithmetic, e.g. `0/0` | 1 |
| `Inf` / `-Inf` | overflow or division by zero | 1 |
| `NULL` | **absence** — no value at all | 0 |

```r
c(1, NA, 3)     # 1 NA 3    ← NA occupies a slot
c(1, NULL, 3)   # 1 3       ← NULL vanishes
1/0             # Inf
-1/0            # -Inf
Inf - Inf       # NaN
0/0             # NaN
```

`NA` is contagious: any arithmetic touching it returns `NA`. Rare exceptions exist where the answer
doesn't depend on the unknown:

```r
any(c(NA, TRUE))   # TRUE — true either way
```

### Integer overflow

```r
.Machine$integer.max     # 2147483647
.Machine$integer.max + 1L  # NA, with a warning
```

Doubles don't overflow this way; they go to `Inf` instead.

## 1.8 Operators

| | | |
|---|---|---|
| `+ - * /` | arithmetic | vectorized |
| `^` | power | `2^10` → `1024` |
| `%%` | remainder | `7 %% 2` → `1` |
| `%/%` | integer division | `7 %/% 2` → `3` |
| `== != < <= > >=` | comparison | return logicals |
| `& | !` | and / or / not | elementwise |
| `&&` `||` | scalar and / or | **not for vectors** |
| `%in%` | membership | `x %in% c(1,2)` |

**Negatives behave as floor division, not truncation:**

```r
-7 %/% 2   # -4   (not -3)
-7 %%  2   #  1   (not -1)
```

R's `%%` takes the sign of the *divisor*. C and its descendants don't. Matters if you port code.

### Floating point

```r
0.1 + 0.2 == 0.3          # FALSE
all.equal(0.1 + 0.2, 0.3) # TRUE
```

Never test numeric equality with `==`. Use `all.equal()`, or compare a difference against a
tolerance. This is the same phenomenon as `sqrt(3)^2 != 3` from Lab 3.

## 1.9 Sequences and repetition

```r
1:5                  # 1 2 3 4 5
5:1                  # 5 4 3 2 1    ← counts DOWN
seq(1, 10, by = 3)   # 1 4 7 10
seq(1, by = 2, length.out = 23)   # first 23 odd numbers
seq_len(5)           # 1 2 3 4 5
seq_along(c("a","b","c"))  # 1 2 3
rep(c(1,2), times = 3)     # 1 2 1 2 1 2
rep(c(1,2), each  = 3)     # 1 1 1 2 2 2
```

**`seq_len` versus `seq`, and why it matters:**

```r
seq(1, 0)      # 1 0        ← counts DOWN. Almost never what you want
seq_len(0)     # integer(0) ← empty, which is what you want
```

Writing `for(i in 1:n)` or `seq(1, n)` breaks silently when `n` is `0`, because you get `c(1, 0)`
instead of nothing. **`seq_len(n)` and `seq_along(x)` are the safe forms.** Question 9 is exactly
this bug.

## 1.10 Vectorization

```r
x <- c(1, 2, 3); y <- c(4, 5, 6)
x * y              # 4 10 18    ← elementwise, "in parallel"
sqrt(c(1,4,9,16))  # 1 2 3 4
```

Most R functions are vectorized. **Prefer them to loops** — shorter, faster, and they handle the
whole vector at once.

## 1.11 Control flow

```r
if(condition){
  do_if_true
} else {           # optional
  do_if_false
}
```

```r
if(x > 10){
  cat("x is very positive.\n")
} else if(x > 0){
  cat("x is a little positive.\n")
} else {
  cat("x is negative.\n")
}
```

**`if` takes one condition, not a vector:**

```r
if(c(TRUE, FALSE)) "a" else "b"
# Error: the condition has length > 1
```

For elementwise choice use `ifelse()`:

```r
ifelse(c(TRUE, FALSE, TRUE), "y", "n")   # "y" "n" "y"
```

> **But `ifelse` evaluates both branches for the whole vector** before selecting. See Question 7 —
> that's how you get a correct answer accompanied by a warning.

### Loops

```r
for(element in vector){
  process_one_at_a_time(element)
}

nums <- c(1, 2, 3, 4, 5)
for(n in nums){
  cat(n, "squared is", n^2, "\n")
}
```

**Start an accumulator at the operation's identity:** `1` for multiplication, `0` for addition,
`-Inf` for maximum. Questions 10 and 12 both turn on this.

## 1.12 Functions

```r
my_absolute_value <- function(x){
  if(x > 0){
    x
  } else {
    -x
  }
}
```

**The last line evaluated is the return value.** No `return` needed.

`return()` overrides and exits immediately:

```r
say_hello <- function(name, scream = FALSE, quiet = FALSE){
  text <- paste("Hello", name)
  if(scream){ text <- paste(toupper(text), "!!!") }
  if(quiet){ return(text) }    # stop here, skip the print
  print(text)
}
```

### Arguments

```r
f <- function(x, y = 2, ...) x^y
f(3)          # 9   — y uses its default
f(3, 3)       # 27  — positional
f(y = 3, x = 2)  # 8 — named, order irrelevant
```

Defaults are **lazy** — evaluated only if used. `function(n = stop("needed"))` doesn't error unless
you actually read `n`.

### Scope

```r
g <- function(x){ x <- x + 1; x }
z <- 5
g(z)   # 6
z      # 5  ← unchanged
```

Functions get **copies**. Assigning inside a function cannot alter the caller's variable. (`<<-`
can, but almost always shouldn't.)

### Early return flattens nesting

Weylandt's three `sign(x)` versions, worst to best:

```r
# 1 — nested if/else inside else: correct, hard to read
# 2 — else if chain: better
# 3 — early return: best
sign <- function(x){
  if(x > 0) return(1)
  if(x < 0) return(-1)
  return(0)
}
```

## 1.13 Useful built-ins

| Purpose | Functions |
|---|---|
| Print with formatting | `print` |
| Print raw, no formatting | `cat` — **no newline unless you add `\n`** |
| Formatted strings | `sprintf("%.3f", pi)` → `"3.142"` |
| Combine strings | `paste` (space), `paste0` (none), `collapse=` |
| String length / case / slice | `nchar`, `toupper`, `tolower`, `substr` |
| Maths | `sqrt`, `sin`, `exp`, `log`, `abs`, `round` |
| Aggregate | `sum`, `prod`, `mean`, `max`, `min`, `range` |
| Elementwise max/min | `pmax`, `pmin` ("parallel") |
| Positions | `which`, `which.max`, `which.min` |
| Ordering | `sort`, `order`, `rev` |
| Missingness | `is.na`, `na.rm = TRUE` |
| Load a package | `library` |

**`sort()` drops `NA` silently:**

```r
sort(c(3, NA, 1))                 # 1 3      ← the NA is gone
sort(c(3, NA, 1), na.last = TRUE) # 1 3 NA
```

Another quiet loss, same family as `filter` in Lab 4.

**`which.max` returns only the first maximum**, which matters when there are ties. Question 8.

### Functional helpers

```r
sapply(1:4, function(i) i^2)   # 1 4 9 16
vapply(1:3, function(i) i*2, numeric(1))   # type-safe sapply
Reduce(`+`, 1:5)               # 15
```

---

# Part 2 — Twelve practice questions

Weylandt's solution is in your notes for all twelve. **The exercise is to find a different route and
work out what each one costs.** Three of the probes find real problems.

---

### Q1 — Describe a vector

```r
f(c(1, 2, 3))   # The vector has 3 elements and is of type numeric
f(1:5)          # The vector has 5 elements and is of type integer
```

**His:**
```r
f <- function(x){
  cat("The vector has", length(x), "elements and is of type", class(x))
}
```

**Try instead:** `paste()` + `print()`; then `sprintf()`.

**Probe:** run `f(c(1,2,3))` twice. What's missing between the outputs? `cat` adds no newline —
which of `cat("...\n")`, `print`, or `message` fixes it?

**Note:** `1:5` reports `integer` while `c(1,2,3)` reports `numeric`. §1.1.

---

### Q2 — `is_even`, vectorized

**His:**
```r
is_even <- function(x) (x %% 2) == 0
```

**Try instead:** `x %% 2 != 1`; `!as.logical(x %% 2)`.

**Probe:** `is_even(c(-4,-3,-2,-1,0,1,2))`. Holds for negatives — because R's `%%` takes the sign of
the divisor, so `-3 %% 2` is `1`. In C it would be `-1` and the function would break. §1.8.

---

### Q3 — `count_even`

**His:**
```r
count_even <- function(x) sum(is_even(x))
```

**Try instead:** `length(which(is_even(x)))`; `table(is_even(x))`.

**Probe:** which version gives you the odd count for free? And what does `mean(is_even(x))` return
— why is that sometimes the more useful number? §1.2.

---

### Q4 — Mean of the first 23 odd numbers

**His:**
```r
mean(seq(from = 1, by = 2, length.out = 23))   # 23
```

**Try instead:** `mean(seq(1, 45, by = 2))`; `mean(2*(1:23) - 1)`.

**Probe:** try `n = 5`, `23`, `100`. The answer is always exactly `n`. And the **sum** of the first
`n` odd numbers is `n^2` — 23 gives 529. The classic proof-without-words: odd numbers stack into a
square. Recognising an identity beats trusting a number.

---

### Q5 — Alternating harmonic series → ln(2)

**His:**
```r
sum(1/seq(1, 1e6) * c(1, -1))   # 0.6931467
log(2)                           # 0.6931472
```

The signs come from **recycling** `c(1, -1)` across a million elements. §1.5.

**Try instead:** `sum((-1)^(0:(1e6-1)) / 1:1e6)` — explicit powers, no recycling.

**Probe — measured:**

| N | sum | error | 1/(2N) |
|---|---|---|---|
| 100 | 0.6881721793 | 4.975e-03 | 5.000e-03 |
| 10,000 | 0.6930971831 | 5.000e-05 | 5.000e-05 |
| 1,000,000 | 0.6931466806 | 5.000e-07 | 5.000e-07 |

**The error is almost exactly `1/(2N)`.** A million terms buys six decimal places. Painfully slow,
and now you can see the rate rather than being told it.

**Second probe:** run with `N = 1000001`. A recycling warning appears and the answer moves — because
the lengths no longer divide evenly. Silent when they do, loud when they don't.

---

### Q6 — `my_mean` without `mean`

**His:**
```r
my_mean <- function(x) sum(x) / length(x)
```

**Try instead:** make it survive `NA`. `sum(x, na.rm=TRUE) / length(x)` is **wrong** — the numerator
drops the missing values but the denominator still counts them. Fix the denominator.

**Probe:** `my_mean(c(1, 2, NA, 4))` against `mean(c(1, 2, NA, 4), na.rm = TRUE)`.

---

### Q7 — `pos_sqrt`: √x if positive, 0 otherwise

**His:**
```r
pos_sqrt <- function(x) sqrt(pmax(0, x))
```

**Try instead:** the `ifelse` version; and one using `x[x < 0] <- 0`.

**Probe:** run the `ifelse` version on `c(-4,-1,1,4,9,-9)`. Right answer **plus** a `NaNs produced`
warning — because `ifelse` evaluates **both** branches across the whole vector before choosing, so
it takes square roots of negatives and throws them away. `pmax` never computes one.

**Right answer, wrong route, visible only in a warning.**

---

### Q8 — `long_string`: longest element

**His:**
```r
long_string <- function(x) x[which.max(nchar(x))]
```

**Try instead:** `x[nchar(x) == max(nchar(x))]`.

**Probe:** run both on `c("ab", "cd", "e")`. They disagree — `which.max` returns the **first**
maximum only, the other returns all ties. Which does the question want? It doesn't say. §1.13.

---

### Q9 — `my_factorial` with `prod`

**His:**
```r
my_factorial <- function(n) return(prod(seq(1, n)))
my_factorial(8)   # 40320
```

**Probe — run this first:**

```r
my_factorial(0)   # 0        ← WRONG. 0! is 1
```

`seq(1, 0)` counts **downwards** and returns `c(1, 0)`, so `prod` hits the zero. The one-word fix is
`seq_len(n)`: `seq_len(0)` is `integer(0)` and `prod(integer(0))` is `1` — the empty product, which
is the mathematically correct convention. §1.9.

**That is a real bug in a solution on the slide.** Finding it is the point.

---

### Q10 — `my_factorial` with a loop

**His:**
```r
my_factorial <- function(n){
  fact <- 1
  for(x in seq(1, n)){ fact <- fact * x }
  return(fact)
}
```

**Probe:** does the loop version have the same `n = 0` problem? Same fix?

**Pattern:** the accumulator starts at `1`, the identity for multiplication. §1.11.

---

### Q11 — Σ 1/k! = e

**His:**
```r
sum(1/factorial(seq(0, 10000)))   # 2.718282
```

**Probe:** try `seq(0, 20)` instead — `2.718281828459045`, identical to fifteen decimals and
identical to `exp(1)`.

Why do 10,000 terms add nothing? `factorial(170)` is `7.26e306`; `factorial(171)` is `Inf`. So
`1/Inf` is `0` and every term past 170 contributes exactly zero — and the terms were already below
double precision by about k = 20.

**Compare with Q5:** a million terms for six digits there, twenty for fifteen here. Linear versus
factorial growth in the denominator.

---

### Q12 — `my_max` without `max`

**His:**
```r
my_max <- function(x){
  max_val <- -Inf
  for(v in x){ if(v > max_val) max_val <- v }
  return(max_val)
}
```

**Answer his "why `-Inf`?" hint:** start at `0` instead and run `my_max(c(-5,-2,-9))`. You get `0`,
which isn't in the vector. `-Inf` is the identity for `max`, exactly as `1` is for `prod`.

**Try instead:** `sort(x)[length(x)]`; `Reduce(function(a,b) if(a > b) a else b, x)`.

**Probe:** `my_max(numeric(0))` returns `-Inf`. So does base `max(numeric(0))`, with a warning.
Accidentally right, or right by design?

---

# Part 3 — The three to report back

1. **`my_factorial(0)` returns 0** — a bug in the posted solution, fixed by `seq_len`
2. **`ifelse` warns where `pmax` doesn't** — same answer, one route computes square roots of
   negative numbers along the way
3. **Q5 needs a million terms, Q11 needs twenty** — and both rates are measurable

---

# Part 4 — Quiet failures, collected

The recurring theme of this course, all in one place:

| What | What you see |
|---|---|
| Type coercion, `c(1, "a")` | nothing — numbers silently become text |
| Recycling, even multiple | nothing |
| Recycling, uneven | a warning |
| `seq(1, 0)` counting down | nothing — you get `c(1, 0)` |
| `sort()` dropping `NA` | nothing |
| `filter()` dropping `NA` rows | nothing |
| `x[9]` out of bounds | `NA`, no error |
| Logical index containing `NA` | an `NA` appears in the output |
| `0.1 + 0.2 == 0.3` | `FALSE` |
| `ifelse` evaluating both branches | a warning, if you're lucky |
| Integer overflow | `NA` plus a warning |

**Only three of these announce themselves.** Checking by relationship — an identity, a known limit,
a second route to the same number — is what catches the rest.

---

## Running notes

| Q | Alternative I wrote | What it cost / gained |
|---|---|---|
| 1 | | |
| 2 | | |
| 3 | | |
| 4 | | |
| 5 | | |
| 6 | | |
| 7 | | |
| 8 | | |
| 9 | | |
| 10 | | |
| 11 | | |
| 12 | | |
