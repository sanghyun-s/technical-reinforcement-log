# Introduction to Testing in Python

| | |
|---|---|
| **Priority** | Tier A · #3 — **the priority course** (closes the last Tier A gap) |
| **Status** | In progress — Ch 1 complete (assert tests, `pytest.raises`, markers, CLI) |
| **Started** | 2026-08-17 |
| **Last worked** | 2026-08-17 |
| **Link** | https://app.datacamp.com/learn/courses/introduction-to-testing-in-python |

---

## What this defends

**The single highest-value course for my §4 weakness.** §12.1's answer claims I *"structure,
test, debug, and verify AI-assisted code."* The follow-up — **"how do you verify it?"** — I
could previously only answer weakly ("I read it carefully"). Ch 1 converts that to the strong
answer: **I write pytest assert tests against the behaviour I specified, and `pytest.raises` for
expected exceptions.** This moves the "I verify AI-generated code" line out of the interview
bank's "Not yet" section — at a foundational level (fixtures/mocking deepen it in later chapters).

---

## Chapter 1 — writing & running tests

**Assert-based tests**
```python
def test_numbers():
    assert multiple_of_two(2) is True
    assert multiple_of_two(4) is False   # verifies specified behaviour
```
A test is a function named `test_*` containing `assert`s. It passes if every assert holds.

**Exception testing — `pytest.raises` (context manager)**
```python
def test_zero():
    with pytest.raises(ValueError):
        multiple_of_two(num=0)           # PASSES iff this raises ValueError
```
The `with pytest.raises(...)` block asserts that the code inside **does** raise — the test
*fails* if the exception is NOT raised. This is how you test the input-validation guards I wrote
in OOP (the salary/balance `raise ValueError` checks).

**Markers (decorators that change test behaviour)**

| Marker | Meaning |
|---|---|
| `@pytest.mark.skip` | Skip **indefinitely**, no matter what (until the mark is removed) |
| `@pytest.mark.skipif(condition)` | Skip **only if** the condition is True (e.g. Python version < 3.4, or `day_of_week == 6`) |
| `@pytest.mark.xfail` | Test is **expected to fail** — verifies a known-failing case |

Conditional skip example: `@pytest.mark.skipif('day_of_week == 6')` with `datetime` to skip on
Saturdays.

**Running from the CLI**
```bash
pytest run_the_test.py            # runs all tests → "2 passed"  ('..' = 2 green dots)
pytest run_the_test.py -k "numbers"   # keyword select → "1 passed, 1 deselected"
```
`-k "name"` runs only tests whose name matches — which is why **meaningful test names matter**.

---

## Fluency notes I caught in the practice (speakable in interview)

Not bugs in the exercises (DataCamp scaffolding), but things I noticed:
- **`list(set(x)) == [1,2,3]` is fragile** — a set is unordered, so relying on the result's
  order is a latent bug. Robust: `sorted(get_unique_values(x)) == [...]`. Passes today, breaks
  silently later — exactly what a test should *not* do.
- **`is True` vs `== True`** — `is True` is an *identity* check (stricter). It works here only
  because `num % 2 == 0` returns a real `bool`; a truthy non-bool would fail `is True` but pass
  plain truthiness. Know the difference.
- **`raise(ValueError)` → `raise ValueError("message")`** — drop the parens around the name,
  add a message so the failure is self-explanatory (the typed-error idea from Intermediate Python).

---

## Direct hits on my own codebase

| Concept | Where it shows up | What I can now do |
|---|---|---|
| `assert` tests vs specified behaviour | Every AI-generated function I ship | Verify output against what I *specified*, not by re-reading |
| `pytest.raises(ValueError)` | PREPARE/LUCENT input validation, OOP balance/salary guards | Test that bad input actually raises, not just returns wrong |
| `-k` keyword selection + CLI run | Any growing test suite | Run a relevant subset fast; names carry meaning |
| `skipif` / `xfail` markers | Environment/version-dependent or known-failing cases | Manage tests honestly instead of deleting/commenting them |

---

## Still unclear (open — later chapters)

- **Fixtures** (`@pytest.fixture`) — setup/teardown, shared test state. The piece that makes the
  "verify AI code" claim *thorough* rather than basic.
- **Parametrization** (`@pytest.mark.parametrize`) — many input/output cases without copy-paste.
- **Mocking** — isolating a unit from its dependencies (APIs, DB) so a test checks one thing.
- These three are what deepen my verification story from "I write asserts" to "I test units in
  isolation against specified behaviour" — target them in the remaining chapters.

---

## Interview sentence

> How do I verify AI-generated code? I write pytest assert tests against the behaviour I
> specified — expected return values — and `pytest.raises` for expected exceptions like bad
> input, then run them from the CLI, selecting subsets with `-k`. I also caught that comparing
> `list(set(...))` to an ordered list is a fragile test, since sets are unordered — the kind of
> silent-pass bug testing exists to prevent.

---

## Follow-up keywords

`pytest.raises` · `assert` · `@pytest.mark.skipif / xfail / skip` · `-k keyword selection` · `fixtures` · `parametrize` · `mocking`
