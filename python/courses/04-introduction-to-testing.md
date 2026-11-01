# Introduction to Testing in Python

| | |
|---|---|
| **Priority** | Tier A · #3 — **the priority course** (closes the last Tier A gap) |
| **Status** | ✅ **Complete** — all 4 chapters (Ch 1 redrilled from the start) · closes the last Tier A gap |
| **Started** | 2026-08-17 |
| **Last worked** | 2026-10-28 (completed) |
| **Link** | https://app.datacamp.com/learn/courses/introduction-to-testing-in-python |

---

## What this defends

**The single highest-value course for my §4 weakness.** §12.1's answer claims I *"structure,
test, debug, and verify AI-assisted code."* The follow-up — **"how do you verify it?"** — I
could previously only answer weakly ("I read it carefully"). Ch 1 converts that to the strong
answer: **I write pytest assert tests against the behaviour I specified, and `pytest.raises` for
expected exceptions.** This moves the "I verify AI-generated code" line out of the interview
bank's "Not yet" section. With Chapters 2–4 done, that answer is now **thorough**, not basic: I can isolate a unit with fixtures, structure the four test types, and run either framework.

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


---

## Chapter 2 — pytest fixtures

A **fixture** = a reusable prepared environment for tests (setup + optional teardown), so tests
don't each rebuild their own data/connections.
```python
@pytest.fixture
def prepare_data():
    return [i for i in range(10)]

def test_elements(prepare_data):          # request by naming it as an argument
    assert 9 in prepare_data
    assert 10 not in prepare_data
```
- **Chain requests** — a fixture can request another fixture (it becomes a *dependency*):
  `setup_data` → `process_data(setup_data)` → `test_process_data(process_data)`. A fixture is
  "dependent" if it's requested by another, "dependable" if it requests one but isn't requested itself.
- **`autouse=True`** — the fixture runs for every test **without being requested**. Use it for
  environment prep that isn't passed in (e.g. `pd.set_option(...)`, a logger, a profiler). *Caveat
  I noted: with `autouse` you can only change variables declared **outside** the fixture (globals/
  options) — you can't hand the test the fixture's return value without requesting it by name.*
- **Teardown with `yield`** — replace `return` with `yield`; code **after** `yield` is the teardown,
  run once the test finishes. Ties straight to the **lazy-evaluation / generator** idea (`yield`
  makes a generator; `next()` drives it):
```python
@pytest.fixture(autouse=True)
def add_numbers_to_list(init_list):
    init_list.extend([i for i in range(10)])   # setup
    yield init_list                            # value handed to the test
    init_list.clear()                          # teardown — don't leak state between tests
```
**Reproduce cold:** a `yield` fixture with setup + teardown; a 2-link chain request; one `autouse` fixture and when it's the right choice.

## Chapter 3 — the four testing types

| Type | Verifies | Example |
|---|---|---|
| **Unit** | one function in isolation | `sum_of_arr([1,2,3]) == 6`; empty list → 0; one element |
| **Feature** | a user-facing feature (may touch several units) | `filter_data_by_manuf(df,'Apple')` returns only Apple rows (`nunique()==1`) |
| **Integration** | several components working together | write a file then assert `os.path.exists(...)` (and clean up) |
| **Performance** | speed, via `pytest-benchmark`'s `benchmark` fixture | `benchmark(find, create_list())` — list vs set lookup |

- **Unit-test design is the "creative part":** decide which units to test, then enumerate cases —
  *regular / empty / single / boundary.* Writing good cases is the skill, not the `assert` syntax.
- **Performance:** `pip install pytest-benchmark`; pass `benchmark` as an argument, call
  `benchmark(func, arg)` or wrap with the `@benchmark` decorator. Showed `set` lookup beating `list`.

**Reproduce cold:** classify a given test as unit/feature/integration; write a unit test covering regular + empty + boundary.

## Chapter 4 — the `unittest` framework (built-in, OOP)

Python's built-in alternative to pytest. **Each test case is a class** subclassing
`unittest.TestCase`; **each test is a method** (`test_*`). (A *test case* = one testing class; a
*test suite* = a collection of them.) This is my OOP course showing up in testing — inheritance +
methods, not loose functions.
```python
import unittest
class TestSquared(unittest.TestCase):
    def test_negative(self):
        self.assertEqual((-3) ** 2, 9)
```
- **Assertion methods** (not bare `assert`): `assertEqual/assertNotEqual`, `assertTrue/assertFalse`,
  `assertIs/assertIsNone`, `assertIsInstance/assertIn`, **`assertRaises`** (context manager:
  `with self.assertRaises(ValueError): func(-5)`).
- **Fixtures = `setUp` / `tearDown`** methods on the class — `setUp()` runs before **each** test,
  `tearDown()` after. **Gotcha the course flags: capital `U` and capital `D`** (`setUp`, `tearDown`)
  — lowercase silently does nothing.
- **CLI:** `python3 -m unittest test_file.py`; flags **`-k "pattern"`** (run matching tests),
  **`-f`** (fail-fast: stop at first failure — "like testing the airplane before a flight"),
  **`-c`** (catch: Ctrl-C once waits + reports, twice raises `KeyboardInterrupt`), **`-v`** (verbose).

**Reproduce cold:** a `unittest.TestCase` with `setUp`/`tearDown` and an `assertRaises`; the pytest↔unittest mapping.

## pytest vs unittest — the one-paragraph answer
Same job, two styles: **pytest** uses plain functions + bare `assert` + decorator fixtures
(`@pytest.fixture`, `yield` teardown) and is terser; **unittest** is **built-in** (no install),
**OOP** (TestCase classes, `assertEqual`-style methods, `setUp`/`tearDown`). pytest for most new
work; unittest when I can't add a dependency or the codebase already uses it. pytest can run
unittest-style tests too.

## What carries into my portfolio / interview
- The "how do you verify AI code?" answer is now **thorough**: asserts + `pytest.raises` + fixtures
  to isolate a unit + the four test-type vocabulary + either framework.
- **Teardown = don't leak state between tests** is the same clean-environment discipline as the
  Quarto/Docker reproducibility lesson — a test that leaves data behind poisons the next one.
- LUCENT's ~145-assertion contract suite now has real vocabulary behind it (unit vs feature vs integration).
