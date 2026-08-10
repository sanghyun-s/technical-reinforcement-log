# Object-Oriented Programming in Python

| | |
|---|---|
| **Priority** | Tier A · #2 (roadmap) |
| **Status** | In progress — Ch 1–3 covered (classes → inheritance → operator overloading) |
| **Started** | 2026-07-30 |
| **Last worked** | 2026-08-08 |
| **Link** | https://app.datacamp.com/learn/courses/object-oriented-programming-in-python |

---

## What this defends

The most direct course for my stated weakness. It explains the machinery my own backend is
built on: a **Pydantic `BaseModel` is a class**, and `class MyModel(BaseModel)` is
**inheritance** — the exact mechanism Ch 2 covers. This is the course that moves the Pydantic
question off my "Not yet" list.

---

## Chapter map

- **Ch 1 — Objects:** classes, `self`, `__init__`, attributes/methods, constructor validation.
- **Ch 2 — Inheritance & class-level data:** class attributes, class methods (alt constructors),
  subclassing, calling the parent constructor, method overriding.
- **Ch 3 — Operator overloading (started):** why objects aren't equal by default, `__eq__`.

---

## Chapter 2 — inheritance & class-level data

**Class attributes** — data shared across all instances, defined in the class body
(`MIN_SALARY = 30000`, `MAX_POSITION = 10`). Used for constants tied to the class (min/max
bounds, `pi` for a Circle). Referenced as `Player.MAX_POSITION`.

**The shadowing gotcha (subtle, worth knowing):** assigning `p1.MAX_SPEED = 7` does **not**
change the class attribute — Python silently creates a *new instance attribute* on `p1` that
shadows it, leaving `p2.MAX_SPEED` and `Player.MAX_SPEED` untouched. To change it for everyone
you must assign `Player.MAX_SPEED = 7`. Instance assignment shadows; it never mutates the class.

**Class methods / alternative constructors** — `@classmethod` with `cls` as first arg. A class
can have only one `__init__`, but `@classmethod` lets you build instances *other* ways and
`return cls(...)`. Practiced `BetterDate.from_str("2020-04-30")` (split + `map(int, ...)`) and
`from_datetime(dt)` — both return `cls(...)`. This is exactly how many libraries expose
`SomeClass.from_something(...)` factory constructors.

**Inheritance** — `class Manager(Employee)`: the subclass gets all the parent's functionality
for free, then customizes.
- Call the parent constructor explicitly: `Employee.__init__(self, name, salary)` — or
  `super().__init__(...)`. Both work; `super()` avoids naming the parent.
- **Override + extend a method** by calling the parent version inside the child:
  `Manager.give_raise(amount, bonus=1.05)` computes `amount * bonus` then defers to
  `Employee.give_raise(self, total_raise)`. Reuse the parent's code, add the twist.
- Class attributes inherit too: `Racer(Player)` with `MAX_SPEED = 5` overrides just that
  constant while inheriting `MAX_POSITION`.

**The portfolio-relevant one — subclassing a real library class:**
```python
class LoggedDF(pd.DataFrame):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)      # pass EVERY DataFrame arg through
        self.created_at = datetime.today()
    def to_csv(self, *args, **kwargs):
        temp = self.copy()
        temp["created_at"] = self.created_at
        pd.DataFrame.to_csv(temp, *args, **kwargs)
```
`*args, **kwargs` catch every parameter of the parent method so I don't recopy DataFrame's huge
signature — the passthrough idiom straight from Intermediate Python. This is the "extend a
library class with domain behavior" move (cf. LUCENT's data handling).

---

## Chapter 3 — operator overloading (started)

**Why objects aren't equal by default:** a variable holds a *reference* to a memory chunk, so
`customer1 == customer2` compares references, not data — two objects with identical data are
"not equal" because they live at different addresses.

**`__eq__`** — like `__init__` runs at creation, `__eq__(self, other)` runs implicitly on `==`.
Override it to compare by *data* (e.g. same customer ID → equal) instead of by identity.

---

## Direct hits on my own codebase

| Concept | Where it shows up | What I now understand |
|---|---|---|
| `class Sub(Parent)` inheritance | **`class MyModel(BaseModel)`** — every Pydantic schema | Why declaring fields gives validation: I inherit BaseModel's machinery |
| `super().__init__(*args, **kwargs)` | FastAPI/Pydantic model init | How a subclass reuses the parent constructor and passes everything through |
| Subclassing a library class | Extending pandas/framework classes | The `LoggedDF` pattern = add domain behavior without rewriting the base |
| `@classmethod` factory | `Model.parse_obj(...)`-style constructors | Alternative constructors that `return cls(...)` |
| `__eq__` overloading | Comparing domain objects by ID | Default `==` is identity, not data — override to compare meaning |

---

## Still unclear (open — internal note)

- The rest of Ch 3: `__hash__` (needed alongside `__eq__` for sets/dict keys), `__str__` vs
  `__repr__`, comparison operators (`__lt__` etc.).
- `super()` vs `Parent.__init__(self, ...)` — when the difference actually matters (multiple
  inheritance / MRO).
- Ch 4 territory: abstract base classes, `@property`.

---

## Interview sentence

> `class MyModel(BaseModel)` is inheritance — my request schema inherits Pydantic's validation
> machinery and I just declare the fields, the same way a `Manager(Employee)` inherits and
> extends `Employee`. I practiced this by subclassing a real `pd.DataFrame` to add a timestamp
> and override `to_csv`, using `*args/**kwargs` to pass every parent argument through — which is
> exactly the "extend a library class with domain behavior" pattern in my own apps.

---

## Follow-up keywords

`inheritance` · `super().__init__` · `@classmethod` · `class vs instance attribute` · `__eq__` · `*args/**kwargs passthrough` · `Pydantic BaseModel`
