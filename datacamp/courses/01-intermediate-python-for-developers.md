# Intermediate Python for Developers

| | |
|---|---|
| **Priority** | 1 of 10 |
| **Status** | Complete |
| **Started** | 2026-07-17 |
| **Completed** | 2026-07-17 |
| **Link** | https://app.datacamp.com/learn/courses/intermediate-python-for-developers |

---

## What this defends

**The gap it closes:**
Independent Python fluency — the core of the weakness reinforcement plan. This is the course
that has to work before any of the others mean anything, because every remaining course
assumes I can read and write Python without a tool drafting it for me.

**The app it connects to:** All three (PREPARE / CASSIA / LUCENT) — every backend is Python.

**The Tier 2 question it moves to Tier 1:**
> "Can you explain the input and output of a Python function in your app?"

Partial credit only. The course covers custom functions, defaults, `*args` / `**kwargs`,
docstrings, and error handling — which is the vocabulary needed to *read* my own function
signatures. It does not cover the async, Pydantic, or FastAPI layers those functions sit in.

---

## Concepts I could not have explained before this course

Not a syllabus copy. Only the things that were actually gaps.

1. **`*args` vs `**kwargs` — and why the asterisks are the mechanism, not decoration.**
   `*args` packs positional arguments into a tuple; `**kwargs` packs keyword arguments into
   a dict. That's why `for arg in args` iterates values directly, but `kwargs` needs
   `.values()` — one is a tuple, the other is a dict. I had seen `**kwargs` in code before
   and treated it as syntax noise.

2. **Docstrings as an interface contract, not a comment.**
   `help()` and `__doc__` read the docstring at runtime. The Args / Returns structure is
   what makes a function usable by someone who never opens its body — including me, six
   months later, reading code I didn't hand-write.

3. **`try/except` vs `raise` — catching an error vs. producing one.**
   `try/except` swallows a failure and keeps going. `raise TypeError(...)` stops execution
   and hands the caller a typed, named failure. The second is usually the responsible
   choice: `clean_text(187)` returning `None` silently is worse than crashing loudly.

4. **A bare `except:` catches everything, including bugs I didn't anticipate.**
   The course's `clean_text` example uses a bare `except`, which would also swallow a
   `KeyboardInterrupt` or a typo-driven `NameError` and print a misleading message about
   data types. `except AttributeError:` would be the honest version.

5. **`lambda` + `map()` returns a lazy map object, not a list.**
   `list(cleaned)` isn't ceremony — the map object is an iterator that produces values on
   demand and is exhausted after one pass.

---

## Direct hits on my own codebase

| Course concept | Where it appears in my app | What I now understand that I didn't |
|---|---|---|
| Default keyword arguments | Function signatures across all three backends | Why some parameters can be omitted at the call site — and that a default makes a parameter optional to the *caller*, not optional to the function |
| Docstrings / `help()` | Throughout the AI-generated code | These weren't decoration the tool added — they're how I read a function I didn't write |
| `raise` with a specific error type | PREPARE row classification, LUCENT input validation | Why bad input should fail loudly at the boundary instead of quietly producing a wrong 1099 pre-review |
| `try/except` | PDF parsing, API calls | Where a fallback is correct (the PDF Skill fallback path) vs. where it hides a real defect |
| `pd.read_csv()`, `.info()`, `.head()` | LUCENT GL CSV ingestion | The first three lines of any GL intake — shape and dtypes before anything else |

---

## Still unclear

Honesty section. These stay Tier 2 until closed.

- **When to reach for a class instead of a function.** The course closed by pointing at OOP
  as the next step and I can feel the gap — Pydantic `BaseModel` is a class and I can't
  currently explain what inheriting from it does. → *Object-Oriented Programming in Python*
- **How to verify a function does what I think it does.** I can write one now. I can't prove
  one. This is the load-bearing gap under my "I test and verify AI-assisted code" claim.
  → *Introduction to Testing in Python*
- **`map()` vs. a list comprehension vs. a `for` loop** — I can write all three; I don't know
  which one a reviewer would expect where, or whether the difference is ever more than style.
- **Which exception type to catch.** I know bare `except` is wrong. I don't yet have the
  vocabulary of specific exceptions to replace it with.
- **Idioms I got wrong in practice:** wrote `if round_result == True:` where Python expects
  `if round_result:`, and `if lower == False:` where it expects `if not lower:`. Also
  shadowed the built-in `list` by using it as a variable name. Fluency isn't only knowing
  what runs — it's writing what a reader expects.

---

## Interview sentence

> I started my reinforcement plan with Intermediate Python for Developers because my portfolio
> was built with heavy AI assistance and I wanted to be able to read my own function
> signatures without help — what `*args` and `**kwargs` actually do to arguments, why a
> docstring is an interface contract, and when a function should raise a typed error instead
> of swallowing one. It closed the reading gap and showed me the next two: I can write
> functions now, but I can't prove they're correct, and I can't yet explain the classes my
> Pydantic schemas inherit from.

**Survives follow-up?** Yes — with one exception. If asked *"so what would you test in
PREPARE?"* I don't have an answer yet. Take Testing before using this sentence in a live
interview.

---

## Follow-up keywords

`pytest` · `Pydantic BaseModel` · `exception hierarchy` · `list comprehension vs map` ·
`type hints` · `PEP 8`
