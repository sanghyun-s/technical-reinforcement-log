# Tier A — Interview Compilation (Python Fluency Block)

**Internal note.** One place to review the whole Tier A foundation before a Python /
fundamentals screen. Synthesises three completed courses by *theme* and *cross-cutting idea*,
not course-by-course — the connections are what interviewers probe.

**Sources:** [01 Intermediate Python](./courses/01-intermediate-python-for-developers.md) ·
[02 Data Structures & Algorithms](./courses/02-data-structures-and-algorithms.md) ·
[03 Object-Oriented Programming](./courses/03-object-oriented-programming.md)
**Pending:** Testing in Python — **not yet taken** (Tier A course, still outstanding). Folds in here once done and logged.

---

## Why this block exists (the 30-second narrative)

I built my portfolio with heavy AI-coding support, and named my gap honestly: **independent
code fluency.** Tier A is the fix — the courses that change what I can *do*, not just describe.
Intermediate Python made me able to **read** my own code; DSA gave me the **structures and Big O**
under every algorithm; OOP explained the **class machinery my backend is built on** (Pydantic,
FastAPI). Testing (pending write-up) closes it by letting me **verify** what I ship.

---

## The interview sentences (promoted, defensible)

Pulled from all three courses — the lines I can say and survive the follow-up.

| Line | From |
|---|---|
| I started with Intermediate Python to read my own function signatures unaided — what `*args`/`**kwargs` do to arguments, why a docstring is an interface contract, when to raise a typed error instead of swallowing one. | 01 |
| I can justify Big O from the data structure up, not memorize it — I built a stack by hand (O(1) push/pop, empty-stack guard) and both binary searches, so I know *why* an approach is O(n) vs O(n²). | 02 |
| `class MyModel(BaseModel)` is inheritance — I declare fields and inherit Pydantic's validation machinery, the same way `Manager(Employee)` inherits and extends `Employee`. I proved it by subclassing a real `pd.DataFrame`. | 03 |
| I use `@property` with a setter to enforce invariants on financial fields — a balance is backed by `_balance` and every assignment runs a non-negative check, so `cust.balance = 3000` validates automatically. Constructor-validation extended to every write. | 03 |
| The Liskov trap: "is-a" inheritance can be wrong — a Square isn't safely a Rectangle — which changed how I decide *when* to subclass at all. | 03 |

---

## Concept cheat-sheet (by theme, across courses)

### Functions & signatures (01)
- `*args` → tuple of positional args; `**kwargs` → dict of keyword args (iterate `.values()`).
- Docstring = runtime interface contract (`help()`, `__doc__`), Args/Returns structure.
- `lambda` + `map()` → lazy iterator; wrap in `list()` to materialise (exhausted after one pass).

### Errors & validation (01 + 03)
- `try/except` swallows and continues; `raise TypeError(...)` fails loud with a typed error — usually the responsible choice at a boundary.
- Bare `except:` catches *everything* (even bugs/`KeyboardInterrupt`) — name the exception.
- Validation lives in the **constructor** (`__init__`) *and* on every write via a `@property` **setter**.

### Classes & OOP (03)
- `self` = the instance handle; `__init__` runs at creation so every object is fully formed.
- **Inheritance:** `class Sub(Parent)`; call parent via `super().__init__(*args, **kwargs)`; override + extend by calling the parent method inside the child.
- `@classmethod` (`cls`) = alternative constructors that `return cls(...)` (e.g. `from_str`).
- `@property` getter + `@x.setter` = controlled access; getter-only = read-only attribute.
- `__eq__` = data equality vs default identity equality.
- **Liskov:** a subclass must be safely substitutable for its parent, syntactically and semantically.
- Class vs instance attribute — and the **shadowing gotcha**: `obj.X = v` creates a *new instance attr* that shadows the class attr; it never mutates the class.

### Data structures (02)
- Linked list: node + `next`; O(1) head insert (no shifting).
- Stack (LIFO): `push`/`pop` O(1), guard the empty case. Queue (FIFO): O(1) enqueue/dequeue on a linked list; `queue.SimpleQueue`.
- Tree: `TreeNode(val, left, right)`; BST insert by ordering. Graph: adjacency dict `{v: [neighbors]}`, weighted → `[target, weight]`; BFS via a queue.

### Algorithms (02)
- Binary search O(log n), iterative + recursive; overflow-safe `mid = lo + (hi-lo)//2`.
- Sorts: bubble/selection O(n²), **merge** O(n log n) (divide & conquer, O(n) space), **quicksort** avg O(n log n) via two-pointer partition.
- Recursion needs a base case that **shrinks the input**; memoization (a cache) collapses exponential recursion to linear.
- Big O = worst case, time *and* space.

---

## Cross-course through-lines (the meta-patterns interviewers reward)

These recur across all three courses — being able to *name* them is senior-level signal.

- **Mutate vs. return.** `give_raise` mutates vs `monthly_salary` returns (03); `sorted()` (new list) vs `.sort()` (in place, returns `None`); a reversed **copy** vs in-place reverse. A design choice, not an accident — appears in every course.
- **`*args/**kwargs` passthrough.** Read args in 01; use it in 03 to forward every parent-method parameter (`super().__init__(*args, **kwargs)`, overriding `to_csv`) without recopying a huge signature.
- **Recursion ⇄ explicit stack.** The call stack *is* a stack; an explicit stack simulates it and dodges recursion-depth limits (DSA + the tree re-drills).
- **"The thing that should move/shrink didn't."** The single most common bug family: merge-sort pointer that doesn't advance, quicksort recursion that doesn't shrink, Fibonacci base case. A variable that must change each iteration silently doesn't.

---

## Portfolio hooks (concept → what it defends)

| Concept | Defends | Tier 2 question it answers |
|---|---|---|
| Inheritance / `BaseModel` | Every Pydantic schema | "Where and why are Pydantic schemas used?" |
| `@property` + setter validation | Financial fields in PREPARE/LUCENT | "How do you stop a balance going negative?" |
| Subclassing a library class | Extending pandas/framework classes | "How did you extend `pd.DataFrame`?" |
| Typed `raise` / constructor validation | Input boundaries across apps | "What happens on bad input?" |
| Big O justification | Any complexity question | "Why is this O(n) and not O(n²)?" |

---

## Bugs I debugged (honest self-record — speakable in interview)

Catching my own bugs *is* the fluency signal. Each is a real one I found and fixed:

- **Factorial-for-Fibonacci** (`n * fib(n-1)` instead of `fib(n-1) + fib(n-2)`) — 02.
- **Merge-sort pointer** that re-read an element because the wrong pointer advanced — 02.
- **Set-vs-dict comprehension** (`{k == v}` builds a set of booleans; needed `{k: v}`) — re-drill.
- **Function-name assignment** (`self.h = set_h` stored the method, not the value) — 03.
- **`count[-1]` silent wrap** on an unguarded `x=0` index — re-drill.

Pattern: most were *silent* (no crash, wrong answer) — which is exactly why tracing beats eyeballing, and why **Testing** (pending) is the natural next layer.

---

## Still open

- **Testing in Python** — **not yet taken** (Tier A, outstanding — the priority course). When done and logged, add: pytest structure,
  assert patterns, fixtures, and the promoted line *"I write pytest cases against the behavior I
  specified before accepting AI-generated code"* — which finally moves that claim out of the
  interview bank's "Not yet" section.
- OOP tail: `__hash__` (with `__eq__`), `__str__`/`__repr__`, abstract base classes, MRO.
