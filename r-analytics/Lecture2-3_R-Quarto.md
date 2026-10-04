> **Coursework · STA 9750 Software Tools for Reproducible Research · Lectures 2–3** · Tag: 📚 **General Coursework**
> Split out of the [Week of 09-14 hub](../2026-09-14_coursework-journal.md). Full sub-journals:
> [Lab3_journal](./Lab3_journal.md) · [Week2_review](./Week2_review.md) · index: [coursework-README](../coursework-README.md)

# STA 9750 Lectures 2–3 — Markdown / Quarto / git + R fundamentals

**Why 📚:** the course core (R, Quarto, Markdown, git for reproducible research) is a degree
requirement off my Python/SQL/AI-builder axis. Real learning, logged for completeness — **not
pitched as portfolio defense.** *But* several lessons transfer directly to my Python work (🔁 below).

## Lecture 2 — Markdown · Quarto · git
The one idea under all three: **analysis and write-up should never be two things kept in sync by
hand.** Markdown removes proprietary formatting; Quarto removes copy-paste between code and doc; git
removes "final_v2_REALLY_final." WYSIWYM (write intent) vs WYSIWYG (manipulate appearance). The
**git box model** — `add` (put in box) → `commit` (seal + label) → `push` (ship) — is exactly the
workflow I already use for this repo.

## Lecture 3 / Lab 3 — R fundamentals
Vectors, `<-`, control flow, packages (`install.packages` once ever / `library()` every session),
custom functions with defaults, recycling. All five exercises done by predicting-then-typing.
(Full detail: [Lab3_journal](./Lab3_journal.md).)

## 🔁 Lessons that transfer straight to my Python/SQL work
R on the surface, universal underneath — worth keeping despite the 📚 tag:
- **Floating-point equality:** `hero_sqrt(3)^2 == 3` is `FALSE` (`2.9999999999999996`);
  `isTRUE(all.equal(...))` is the real test. **Identical to Python's `0.1+0.2 != 0.3`** — tolerance
  for computed floats, `==` only for ints/strings. (Ties to my Testing course.)
- **Silent recycling / vectorization bugs** — R warns about *ragged* recycling, not *wrong*
  recycling. Same silent-broadcast trap as pandas/numpy. "Silence means the arithmetic was tidy,
  never that I got what I meant."
- **`seq_len(n)` over `1:n`** — `1:0` is `c(1,0)` (loops twice on empty!). Same empty-range guard
  family as the `default=0` empty-group guard from LC 3541.
- **Mutate vs. return, and scope leakage** — a loop's `i` leaks to global; a function's args don't.
  Same mutate-vs-return distinction running through my OOP and LeetCode notes.
- **I caught the instructor's own bug:** his Exercise 5 `hero_sqrt` uses `1:iter`, so
  `hero_sqrt(3, iter=0)` returns two iterations instead of the starting guess. `seq_len(iter)`
  fixes it. (Documented in [Lab3_journal](./Lab3_journal.md).)
