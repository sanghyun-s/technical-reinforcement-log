# Object-Oriented Programming in Python

| | |
|---|---|
| **Priority** | Tier A · #2 (roadmap) |
| **Status** | ✅ Complete — all 4 chapters |
| **Started** | 2026-07-30 |
| **Completed** | 2026-08-10 |
| **Link** | https://app.datacamp.com/learn/courses/object-oriented-programming-in-python |

---

## What this defends

The most direct course for my stated weakness. It explains the machinery my own backend is
built on: a **Pydantic `BaseModel` is a class**, `class MyModel(BaseModel)` is **inheritance**,
and **`@property`** is how validated attribute access works. Moves the Pydantic question — and
now the "how do you enforce invariants on financial fields" question — off my "Not yet" list.

---

## Chapter map

- **Ch 1 — Objects:** classes, `self`, `__init__`, attributes/methods, constructor validation.
- **Ch 2 — Inheritance & class-level data:** class attributes, `@classmethod` (alt constructors),
  subclassing, parent constructor, method overriding, subclassing a real `pd.DataFrame`.
- **Ch 3 — Operator overloading:** identity vs. data equality, `__eq__`.
- **Ch 4 — Best practices:** Liskov Substitution Principle, internal attributes (`_name`
  convention), `@property` getters/setters, read-only attributes.

---

## Chapter 4 — design principles & properties

**Liskov Substitution Principle (LSP)** — a base class must be interchangeable with any
subclass without breaking the surrounding program. Both *syntactically* (compatible method
signatures) and *semantically* (a subclass method shouldn't demand stronger input conditions,
give weaker outputs, or throw new exceptions).
- **The Rectangle/Square trap:** `Square(Rectangle)` *looks* like clean inheritance but
  **violates LSP** — a Square can't allow `h` and `w` to change independently, so substituting
  a Square where a Rectangle is expected breaks the program. Fix: override `set_h`/`set_w` in
  Square so both dims stay equal. **Lesson: "is-a" inheritance can be wrong even when it feels
  natural** — a real design-judgment point.

**Internal attributes** — the single-underscore `_name` convention signals "internal, don't
touch from outside." Practiced `_MAX_DAYS`/`_MAX_MONTHS` class constants and an `_is_valid()`
method kept off the public interface.

**`@property` — controlled attribute access.** Back a public name with an internal
`_balance`, expose a `@property` getter, and a `@balance.setter` that validates on every
assignment:
```python
class Customer:
    def __init__(self, name, new_bal):
        self.name = name
        if new_bal < 0: raise ValueError("Invalid balance!")
        self._balance = new_bal
    @property
    def balance(self):          # getter — read via cust.balance
        return self._balance
    @balance.setter
    def balance(self, new_bal): # setter — runs on cust.balance = 3000
        if new_bal < 0: raise ValueError("Invalid balance!")
        self._balance = new_bal
```
So `cust.balance = 3000` looks like a plain assignment but runs validation. Combined with a
getter-only (no setter), you get a **read-only attribute** (used to lock `_created_at` on the
`LoggedDF`). This extends Ch1's constructor-validation to *every* assignment, not just creation.

**Bugs I caught and fixed along the way (self-recorded):**
- `self.h = set_h` — assigned the *function name* instead of the parameter `h`. Overwrote the
  value with a function reference. → `self.h = h`.
- `def set_w(self, w, h)` — added a parameter the interface didn't allow, so `square.set_w(5)`
  crashed with a missing-argument `TypeError`. Interface contracts matter.
- Indentation mismatch on the methods — the tabs-vs-spaces class of `IndentationError`.

---

## Direct hits on my own codebase

| Concept | Where it shows up | What I now understand |
|---|---|---|
| `class Sub(Parent)` inheritance | `class MyModel(BaseModel)` | Declaring fields gives validation because I inherit BaseModel's machinery |
| `@property` + setter validation | Financial fields (balance, amount) in PREPARE/LUCENT | How to enforce "never negative" on *every* assignment, not just at creation |
| Read-only attribute (getter, no setter) | Audit trail / created_at style fields | Lock a value after construction so downstream code can't mutate it |
| Liskov Substitution | Any subclass in my code | "is-a" can be wrong — a subclass must be safely substitutable, or don't inherit |
| `_internal` convention | Private helpers in my services | Signalling "not public interface" without a language-enforced private |

---

## Still unclear (open — internal note)

- `__str__` vs `__repr__` and the other comparison dunders (`__lt__` etc.) — `__eq__` covered,
  the rest named but not drilled.
- `__hash__` alongside `__eq__` (needed to keep objects usable as set/dict keys).
- Abstract base classes / `abc` — mentioned in passing, not practiced.
- Multiple inheritance / MRO — where `super()` vs `Parent.__init__` actually diverges.

---

## Interview sentence

> `class MyModel(BaseModel)` is inheritance — I declare fields and inherit Pydantic's validation.
> And I use `@property` with a setter to enforce invariants on financial fields: a balance is
> backed by `_balance`, and every assignment runs a non-negative check, so `cust.balance = 3000`
> validates automatically. That's constructor-validation extended to every write. I also learned
> the Liskov trap — that "is-a" inheritance can be wrong (a Square isn't safely a Rectangle),
> which changed how I decide when to subclass at all.

---

## Follow-up keywords

`@property / setter` · `Liskov Substitution` · `read-only attribute` · `_internal convention` · `inheritance` · `Pydantic BaseModel` · `__str__ / __repr__`
