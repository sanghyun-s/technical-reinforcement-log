# Object-Oriented Programming in Python

| | |
|---|---|
| **Priority** | Tier A · #2 (roadmap) |
| **Status** | In progress — Ch 1 complete (classes, `self`, `__init__`, attributes/methods) |
| **Started** | 2026-07-30 |
| **Last worked** | 2026-07-30 |
| **Link** | https://app.datacamp.com/learn/courses/object-oriented-programming-in-python |

---

## What this defends

The single most direct course for my stated weakness. It explains the machinery my own
backend is built on: a **Pydantic `BaseModel` is a class**, and FastAPI dependency injection
sits on the same foundation. Directly targets the Tier 2 question *"where and why are Pydantic
schemas used?"* — the one I currently can't fully answer.

---

## Chapter 1 — classes and objects

**Concepts covered**
- **Exploring a class** — `type()`, `dir()`, `help()` to read an object's interface before
  touching it. (`help(x)` prints the docstring — same interface-contract idea from Intermediate
  Python.)
- **`self`** — the handle to the specific instance a method is called on. Every method takes it
  first; it's how `emp.give_raise(1500)` knows *which* employee's salary to change.
- **Setter methods** — `set_name`, `set_salary` assign attributes. Methods are just functions
  bound to the object, so they can set, return, print, or raise — whatever makes sense as the
  object's behavior.
- **Attributes aren't read-only** — `emp.salary = emp.salary + 1500` works directly, but
  "raise a salary" is a *recurring behavior*, so it belongs in the class as `give_raise()`
  rather than being repeated at call sites.
- **Return vs. mutate** — `give_raise()` **mutates** `self.salary`; `monthly_salary()`
  **returns** `self.salary / 12` without changing state. Same distinction I met in the BST
  re-drill (mutate an accumulator vs. return a subtotal) — now in class form.
- **`__init__` constructor** — runs automatically at object creation, so every object has its
  attributes the moment it exists. `__init__(self, name, salary=0)` with a default parameter.
- **Validation in the constructor** — checked `if salary > 0 … else 0 + "Invalid salary!"`.
  Preprocessing at creation time so a bad object can't come into existence half-formed.
- **Derived attribute** — `self.hire_date = datetime.today()`: not every attribute comes from a
  parameter; some are computed at construction.

**From-scratch class (no bugs):** wrote a `Point` class — `__init__(self, x=0.0, y=0.0)`,
`distance_to_origin()` returning `(x**2 + y**2)**0.5`, and `reflect(axis)` that negates `y` for
`"x"` and `x` for `"y"`. The axis-swap subtlety (reflecting across x flips the *y* coordinate)
and the distance formula were both correct.

---

## Direct hits on my own codebase

| Concept | Where it shows up | What I now understand |
|---|---|---|
| `__init__` runs at creation | Every Pydantic model instantiation | Why a request object always has its fields — construction guarantees it |
| Validation in the constructor | FastAPI request-body validation | Pydantic rejects bad input because validation runs at construction — my salary check is the manual version |
| `self` / instance state | Any class in my backend | How a method knows which object's data it's touching |
| Return vs. mutate methods | Service functions across apps | Whether a method changes state or just computes — a design choice, not an accident |

---

## Still unclear (open — internal note)

- Inheritance — the mechanism behind `class MyModel(BaseModel)`. Ch 2+ territory; this is the
  piece that fully closes the Pydantic question.
- Class attributes vs. instance attributes (shared across all instances vs. per-object).
- `@classmethod` / `@staticmethod` / `@property` — named but not yet practiced.

---

## Interview sentence

> A Pydantic `BaseModel` is a class whose `__init__` validates and assigns attributes at
> creation time — I just declare the fields instead of writing the constructor by hand, and
> the validation runs the moment the request object is built. Practicing constructors and
> in-constructor validation on a plain `Employee` class is what made that concrete for me.

---

## Follow-up keywords

`__init__ constructor` · `self` · `instance vs class attribute` · `inheritance` · `Pydantic BaseModel` · `@property`
