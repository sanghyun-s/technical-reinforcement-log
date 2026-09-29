# DataCamp Summer 2026 — Active-Review & Retrieval Document

**Rebuilt from two Korean source notes, translated to English and expanded for active recall.**
Sources: `DataCamp_Python_Study_Review_2026_Summer (KOR)` (a reflective "V2" review) and
`Python 여름 동안 들은 트DataCamp 코스 4개 노트` (the raw original notes the V2 was reviewing).

The goal is **not** a summary. It's a document I can return to months from now to *rebuild* the
knowledge, practice it cold, defend it in an interview, and connect it to my current work. Each
topic is structured six ways:

1. **Taught** — what the course presented
2. **Practiced** — what I actually implemented
3. **Mistakes & debugging** — errors, corrections, and debugging steps in my notes
4. **Principle** — the underlying programming idea
5. **Connects to** — coursework, portfolio (PREPARE/CASSIA/LUCENT), reinforcement log, job prep
6. **Reproduce cold** — what I should be able to write, explain, or solve without the answer

### Provenance legend (applied per section)

- ✅ **Confirmed from source notes** — present in the two DataCamp files above.
- 🔁 **Recovered from another provided study record** — not in the two files, but reconstructed
  from *my own* other materials in this chat (earlier uploads / course write-ups), marked inline.
- 🕳️ **Needs active review / cold recall** — documented, but I should re-derive it unaided.
- ❌ **Missing from available materials** — not reconstructable from anything I've provided.

### Coverage map

| Course | Coverage in the two files | Status |
|---|---|---|
| **1 — Intermediate Python for Developers** | Full (6 topics + recap) | ✅ Confirmed |
| **2 — Data Structures & Algorithms in Python** | Full (Ch 1–4, 12 topics + recap) | ✅ Confirmed |
| **3 — Object-Oriented Programming in Python** | **Chapter 1 only** | ✅ Ch 1 · 🔁 Ch 2–4 recovered |
| **4 — Introduction to Testing in Python** | **Absent** | 🔁 Ch 1 recovered · ❌ Ch 2+ missing |

> The two source files explicitly stop at OOP Chapter 1 (the note itself says Chapters 2–4 must
> be recovered from separate originals). OOP Ch 2–4 and Testing Ch 1 below are recovered from my
> **own** earlier uploads in this chat and are tagged 🔁; they are not from the DataCamp files and
> not invented.

---

# Course 1 — Intermediate Python for Developers  ✅

*Framed in my notes as "grad-level programming, overlaps CIS 9650." Recap sequence: built-in
functions/modules/packages → custom functions → default arguments → docstrings → arbitrary
arguments (`*args`/`**kwargs`) → lambda/map → error handling.*

## 1.1 Python ecosystem: built-ins, modules, packages  ✅

**Taught.** You don't write everything yourself. Built-ins (`min`, `max`, `len`, `sum`,
`sorted`) exist immediately; the standard library adds modules (`os`, `string`); external
packages install via `python3 -m pip install <package>`.

**Practiced.** Applied built-ins with no hints. Used `os.getcwd()` (current working directory)
and `os.environ` (environment variables); `string.ascii/digits/punctuation`. Reconfirmed pandas
as a data-manipulation package: `pd.DataFrame()`, `pd.read_csv()`, `.head()`, `.info()`.

**Mistakes & debugging.** None recorded here.

**Principle.** The developer's real choice: *implement it myself vs. use a tested abstraction.*
Distinctions to keep straight — **built-in function** (ships with Python), **module** (functions
+ variables in one namespace/file), **package** (a larger structure containing modules),
**library** (loose term for a purpose-built collection).

**Connects to.** Portfolio: don't hand-implement transformations pandas already does — *but*
never copy-paste a package call without knowing what it does. Interview: "Why pandas instead of
writing the transformation yourself?" → *"pandas provides tested, readable abstractions for
tabular data; I use it where custom code would add complexity without business value."*

**Reproduce cold.** Explain built-in vs module vs package vs library; how to install and import
an external package; why choosing a package is a design decision, not just convenience.

## 1.2 Custom functions & default arguments  ✅

**Taught.** `def name(args): <logic>; return`. Default arguments:
`def calculate_discount(price, discount_percent=15, round_result=True):`.

**Practiced (actual code from the raw note):**
```python
def calculate_discount(price, discount_percent=15, round_result=True):
    """Calculate the discounted price of a product."""
    discounted_price = price - (price * (discount_percent / 100))
    if round_result == True:
        return round(discounted_price, 2)
    else:
        return discounted_price
print(calculate_discount.__doc__)
```

**Mistakes & debugging.** None recorded, but note (later understanding, from the reinforcement
log): `if round_result == True:` is non-idiomatic — Python expects `if round_result:`. Kept here
as a review flag, not as a correction that appeared in the original note.

**Principle.** A function turns repeated behavior into *one named contract* — reusable,
input/output separated, testable, readable. Defaults are API design: *make the common path
simple, allow override only when needed.* `calculate_discount(price)` uses defaults;
`calculate_discount(price, discount_percent=20, round_result=False)` overrides.

**Connects to.** FastAPI/Pydantic endpoints use default/optional parameters the same way — the
"simple common path, override when needed" mindset is how to read my own backend.

**Reproduce cold.** Write a function with a required arg + two defaults; explain why defaults are
an API-design choice, not just input-skipping.

## 1.3 Docstrings — code as an explainable interface  ✅

**Taught.** A docstring is text describing what a function does, surfaced via `help()` / `__doc__`.
Single-line and multi-line (Args/Returns) forms.

**Practiced (actual code):**
```python
def clean_text(text, lower=True):
    """
    Clean text by swapping spaces to underscores and converting to lowercase.
    Args:
        text (str): A string to be cleaned.
        lower (bool): Whether to convert the text to lowercase.
    Returns:
        text (str): Cleaned string.
    """
    clean_text = text.replace(' ', '_')
    if lower == False:
        return clean_text
    else:
        return clean_text.lower()
print(help(clean_text))
```

**Mistakes & debugging.** None recorded. (Review flag: `if lower == False:` → idiomatic `if not lower:`.)

**Principle.** **Implementation and contract are separate.** A caller should know what to pass,
what comes back, and what options exist *without reading the body*.

**Connects to.** Core to defending PREPARE/CASSIA/LUCENT: even with heavy AI-assisted coding, I
can state each function/endpoint's input, output, validation, and failure mode — that's the
difference between *generating* code and *owning* it.

**Reproduce cold.** Write a multi-line docstring with Args/Returns; explain why the contract is
independent of the implementation.

## 1.4 `*args` and `**kwargs`  ✅

**Taught.** `*args` collects positional arguments into a **tuple**; `**kwargs` collects keyword
arguments into a **dict**.

**Practiced (actual code):**
```python
def concat(*args):                       # args is a TUPLE
    result = ""
    for arg in args:
        result += " " + arg
    return result
print(concat("Python", "is", "great!"))

def concat(**kwargs):                    # kwargs is a DICT — iterate .values()
    result = ""
    for kwarg in kwargs.values():
        result += " " + kwarg
    return result
print(concat(start="Python", middle="is", end="great!"))
```

**Mistakes & debugging.** None recorded.

**Principle.** `*args` = position-centric, variable count; `**kwargs` = name-centric, key-value,
variable count. Essential for *reading* framework/library code (FastAPI, ML libs, decorators,
config-heavy APIs) where optional parameters abound.

**Connects to.** Reappears in OOP Ch 2 (recovered below): `super().__init__(*args, **kwargs)`
forwards every parent-constructor argument without recopying the signature.

**Reproduce cold.** Write both a `*args` and a `**kwargs` function; state which is a tuple vs a
dict and why `kwargs` needs `.values()`.

## 1.5 Lambda and `map()`  ✅

**Taught.** `lambda` is an anonymous (unnamed) function for short transformations, often with
`map()`.

**Practiced.** Applied an extra-space ratio to a file size; cleaned names —
conceptually `lambda x: x.replace(" ", "_").lower()` combined with `map()`.

**Mistakes & debugging.** None recorded.

**Principle (a judgment, added in V2).** Lambda is good for short, single-use transforms; for
long or reusable logic a named `def` reads better. **Good code isn't short code — it's code whose
intent is clearest.**

**Connects to.** Reinforcement-log echo: I keep meeting "the shortest tool isn't always the right
one" (counting-sort vs plain sort, matrix expo vs O(n)). Same judgment.

**Reproduce cold.** Write a `lambda` + `map()` one-liner; then argue when a named function is the
better choice.

## 1.6 Errors and debugging — read the cause, don't silence it  ✅

**Taught.** Late-course: syntax/error handling. Passing an unsupported argument to
`requests.get()` isn't "the code is broken" — the error message + function contract tell you the
argument isn't supported. `clean_text(187)` (int where str expected) compared `try/except`
against `raise TypeError(...)`.

**Practiced.** Compared the two error strategies on the wrong-type input.

**Mistakes & debugging (the lesson itself).** The distinction I must re-internalize:
- `try/except` = **control flow reacting to an exception that occurred.**
- `raise` = **deliberately creating an exception when I detect an invalid state.**
They are not the same thing.

**Principle.** Fail *loud and typed* at the boundary rather than swallowing silently.

**Connects to (explicit in V2).** Directly my AI-assisted-coding weakness: when AI-generated code
errors, "I asked Claude to fix it" is not a method. The method is: read the traceback location →
check the function signature → check input types → define expected behavior → fix → re-test.

**Reproduce cold.** Explain `try/except` vs `raise`; write a function that raises `TypeError` on
bad input; describe the 5-step debugging order above.

## Course 1 — recall checklist (from the note) 🕳️

I should be able to answer, unaided: function vs method · module vs package · why default
arguments · `*args` vs `**kwargs` · when lambda over a named function · `try/except` vs `raise` ·
why docstrings matter. *If any of these stall, the course is "completed" but retrieval is not.*

---

# Course 2 — Data Structures and Algorithms in Python  ✅

*The largest, most content-heavy course; about learning **problem-solving structure**, not just
syntax. Recap sequence: Ch 1 (linked list, stack, Big O) → Ch 2 (queue, hash table, tree, graph,
recursion) → Ch 3 (linear/binary search, DFS/BFS, BST) → Ch 4 (bubble/selection/insertion/merge/
quick sort).*

## 2.1 Algorithm ↔ data structure relationship  ✅

**Taught / Principle.** An **algorithm** is an instruction sequence to solve a problem; a **data
structure** decides how the data it processes is stored and accessed. They can't be separated —
list vs dict/hash vs stack vs queue vs tree changes both the approach and the complexity. The
course's biggest lesson: **think "which structure fits this problem" before writing code.**

**Connects to.** The exact instinct the reinforcement re-drills reward.

**Reproduce cold.** Given a problem, argue which structure fits and why, before coding.

## 2.2 Linked list  ✅

**Taught.** Unlike an array/list (contiguous memory), each **node** holds `data/value` + a `next`
pointer; the list tracks `head` and `tail`. Core ops: insert-at-beginning, insert-at-end, remove,
search.

**Practiced.** Implemented the node-based structure and its operations.

**Principle.** **Operation cost depends on the structure** (e.g., O(1) head insert, no shifting).

**Connects to.** Not daily Data-Analyst work, but the cost-awareness is the point; reinforced by
the reinforcement-log linked-list re-drills (e.g., LC 1265).

**Reproduce cold.** Describe a node (`value` + `next`); why head-insert is O(1) vs an array shift.

## 2.3 Big O — not "does it run" but "how does it scale"  ✅

**Taught.** Worst-case complexity, in time and space. Compared O(1), O(n), O(n²): index access ≈
constant; one full pass = O(n); nested loops over all pairs = O(n²).

**Principle / habit.** Correct output ≠ good solution. Always ask **"what happens if the input is
10× larger?"**

**Connects to.** LeetCode *and* analytics: any code runs on 10 rows; on 10M rows the algorithm /
join strategy decides feasibility. (Ties to CIS 9760 Big Data.)

**Reproduce cold.** State the Big O of a loop / nested loop / dict lookup and justify it.

## 2.4 Stack — LIFO  ✅

**Taught.** Last-In-First-Out; ops `push` / `pop`. Implemented a linked-node stack; also used
Python's `queue.LifoQueue`.

**Principle / applications.** Function call stack, undo history, DFS, backtracking.

**Connects to.** Reinforcement log: recursion↔explicit-stack duality (invert tree, inorder, LC 1265).

**Reproduce cold.** Implement push/pop with the empty-stack guard; name three LIFO applications.

## 2.5 Queue — FIFO  ✅

**Taught.** First-In-First-Out. `enqueue()` adds at back, `dequeue()` removes at front (printer-
task example). With head/tail on a linked list, both are **O(1)**. Also used `queue.SimpleQueue`.

**Principle / applications.** Job processing, message queues, task scheduling, API request
handling.

**Connects to (explicit in V2).** Likely to reappear in AWS/Kafka-style coursework (CIS 9760).

**Reproduce cold.** Explain FIFO; why linked-list head/tail gives O(1) enqueue/dequeue.

## 2.6 Hash table / dictionary  ✅

**Taught.** `dict` gives key→value lookup (hash-based). Practiced `.items()`, `.keys()`,
`.values()`, and nested-dictionary iteration.

**Principle.** Average **O(1)** lookup — the key advantage.

**Connects to.** LeetCode Two Sum (same principle); analytics uses: ID→attribute mapping, config,
JSON, frequency counting. Reinforcement log: `Counter` vs `defaultdict(set)` judgment (LC 3541/3450).

**Reproduce cold.** State: key-value / fast lookup / average O(1) / use for membership or mapping.

## 2.7 Trees  ✅

**Taught.** Binary tree: each node may have left/right children. Corrected a broken `TreeNode` so
`data`, `left_child`, `right_child` linked correctly.

**Principle.** Data can be **hierarchical**, not just a linear sequence.

**Mistakes & debugging.** The exercise *was* fixing a wrong TreeNode implementation.

**Connects to.** BST/tree re-drills are now my warmest reinforcement family (LC 938, 226, 3831, 94, 1382, 1302).

**Reproduce cold.** Write a `TreeNode(data, left, right)`; explain why trees model hierarchy.

## 2.8 Graphs  ✅

**Taught.** Vertices + edges. Used a `vertices = {}` adjacency structure; for a **weighted**
graph, stored adjacent vertex + weight (cities: Paris → Toulouse → Biarritz with distances).

**Principle.** Model network relationships, dependencies, workflow connections.

**Connects to.** Reinforcement log: adjacency-matrix degree = row sum (LC 3898).

**Reproduce cold.** Represent a small weighted graph as an adjacency dict.

## 2.9 Recursion  ✅

**Taught.** A function calling itself; the two essentials are the **base case** and the
**recursive step**. A wrong base case never terminates.

**Practiced / Mistakes & debugging.** *Towers of Hanoi*: wrong base case / recursive calls
exceeded maximum recursion depth. *Fibonacci*: plain recursion, then a **cache** to store solved
subproblems.

**Principle.** Recursion terminates only if **every call shrinks the problem toward the base
case.** Caching repeated subproblems = **memoization / dynamic programming**.

**Connects to.** Reinforcement log's recurring bug family: "the thing that should shrink/move
didn't" (merge-sort pointer, quicksort recursion). DP collapse (LC 338 `ans[i>>1]+(i&1)`).

**Reproduce cold.** Write recursive Fibonacci + a memoized version; explain why a base case that
doesn't shrink the input causes infinite recursion.

## 2.10 Searching  ✅

**Taught.** *Linear search* — check one by one, O(n). *Binary search* — on **sorted** data, check
the middle, halve the range each step. Implemented both iterative and recursive binary search.

**Principle.** Binary search's value isn't the code — it's the idea: **use a condition to halve
the search space repeatedly.** Prerequisite: input must be ordered.

**Connects to.** Reinforcement log: LC 1382 rebuilds a balanced BST as "binary-search midpoint
logic run backwards."

**Reproduce cold.** Write iterative binary search (overflow-safe `mid = lo + (hi-lo)//2`); state
the sorted-input prerequisite.

## 2.11 DFS and BFS  ✅

**Taught.** DFS goes deep down one branch; BFS explores one level first. **DFS → stack/recursion;
BFS → queue.** This reconnects the earlier stack/queue material to search — DSA topics are not
isolated.

**Connects to.** Reinforcement log: LC 3831 / 1302 (BFS level traversal), LC 226 (DFS vs BFS
differ only by `pop()` vs `popleft()`).

**Reproduce cold.** State DFS↔stack, BFS↔queue, and why.

## 2.12 Sorting algorithms  ✅

**Taught.** Bubble, Selection, Insertion, Merge, Quick. The point isn't memorizing five
implementations — it's understanding the **strategy differences.**
- **Bubble** — repeatedly compare/swap adjacent values; intuitive, inefficient.
- **Selection** — find the min of the remaining region, place it at the front.
- **Insertion** — insert the current value into its place in the already-sorted prefix.
- **Merge** — divide and conquer: divide → sort each → merge (combine).
- **Quick** — partition around a pivot into smaller/larger, recurse on each partition.

**Mistakes & debugging (high review value — both are real bugs I fixed):**
- **Merge sort — pointer bug.** When copying the remaining elements from `left_half` (and
  `right_half`), incrementing the *wrong* pointer (`i` vs `j`) makes the loop re-access the same
  element until the index runs off the end → **IndexError**. Fix: each drain loop advances **its
  own** pointer. *(The raw note preserves the instructor's correction text: "update the correct
  pointer in each loop.")* The lesson: "the algorithm's logic was right, but the pointer moved
  wrong."
- **Quick sort — infinite recursion.** Passing the *same full index range* into the recursive
  call means the problem size never shrinks → infinite recursion. Fix: recurse on the sub-ranges.

**Principle (the biggest takeaway of the course).** **For recursion to terminate, every call must
shrink the problem toward the base case** — true for quicksort and all recursive logic.

**Connects to.** Reinforcement log's "asymptotically-best isn't always constraint-appropriate"
and the "shrink/advance" bug family.

**Reproduce cold.** Explain each sort's strategy in one line; write the merge step with correct
independent pointer advancement; state why quicksort needs shrinking sub-ranges.

## Course 2 — recall checklist (from the note) 🕳️

Don't rewrite every algorithm from scratch. For each structure, be able to say four things:
**what it is / what operation it's good at / rough complexity / which problem selects it.**
Example target answer — *Hash map:* key-value structure / fast lookup / average O(1) / membership
or mapping problem.

---

# Course 3 — Object-Oriented Programming in Python

## Chapter 1  ✅ (confirmed from source notes; "Note 7/30 OOP Chapter 1")

### 3.1 Exploring an existing object first  ✅
**Taught / Practiced.** Before writing a class, inspect existing objects: `type()` (its class),
`dir()` (available attributes/methods), `help()` (documentation).
**Principle / Connects to.** A self-service exploration order *before* asking AI:
**`type` → `dir` → `help` → official docs.**
**Reproduce cold.** Given an unknown object, list the three inspection functions and what each shows.

### 3.2 Class vs object  ✅
**Taught.** A **class** is a template for making objects; an **object** is an instance of a class.
`Employee` is the class; `emp = Employee()` is an instance.
**Reproduce cold.** Define class vs instance in one sentence each.

### 3.3 Attributes vs methods  ✅
**Taught.** **Attribute** = the object's state/data (`employee.name`, `employee.salary`);
**method** = behavior (`employee.give_raise()`).
**Design insight (from the exercise).** You *could* mutate `emp.salary += 1500` directly, but if
"give a raise" is a recurring business behavior, `emp.give_raise(1500)` as a method is more
natural. **OOP's basic purpose: bundle data with the behavior related to that data.**
**Reproduce cold.** Explain attribute vs method; argue why a recurring mutation belongs in a method.

### 3.4 `self`  ✅
**Taught.** In an instance method, `self` refers to the current object; `self.salary` is *this*
Employee's salary. Without this concept, class code is just memorized syntax.
**Reproduce cold.** Explain what `self` binds to and why methods take it first.

### 3.5 Constructor `__init__()` + validation  ✅
**Taught / Practiced.** `__init__()` runs automatically at object creation. The `Employee`
exercise set `name` and `salary` at creation, and put **validation/preprocessing** in the
constructor: if `salary <= 0`, set it to `0`. Goal: an object holds all needed state the moment
it exists.
**Design question (deepened in V2).** For invalid input: silent-correct, fall back to a default,
or raise an exception? A recurring portfolio decision.
**Connects to (explicit).** **Pydantic** models in PREPARE/CASSIA/LUCENT are the same idea — data
object creation, field definition, input validation, blocking invalid state. *OOP study is the
base for understanding my own FastAPI/Pydantic backend, not just "class interview prep."*
**Reproduce cold.** Write an `__init__` with a validation guard; explain the
silent-correct / default / raise trade-off.

### 3.6 Writing a class from scratch — `Point`  ✅
**Practiced (actual code from the raw note):**
```python
class Point:
    def __init__(self, x=0.0, y=0.0):
        self.x = x
        self.y = y
    def distance_to_origin(self):
        return (self.x**2 + self.y**2)**0.5
    def reflect(self, axis):
        if axis == "x":
            self.y = -self.y
        elif axis == "y":
            self.x = -self.x
        else:
            print("Error: Invalid axis specified!")
```
Verification the note kept: `Point(x=3.0)`, `reflect("y")` → `(-3.0, 0.0)`; then `pt.y = 4.0`,
`distance_to_origin()` → `5.0`.
**Principle.** The subtlety isn't the formula — it's **designing one object that holds its own
state and the behavior related to it.** (Note the reflect axis-swap: reflecting across `x` negates
`y`.)
**Reproduce cold.** Write `Point` with `distance_to_origin` and `reflect` from scratch.

## Chapters 2–4  🔁 Recovered from another provided study record

> **Not in the two DataCamp files.** Reconstructed from my *own* OOP Chapter 2–3 and Chapter 4
> notes uploaded earlier in this chat (captured in `datacamp/courses/03-object-oriented-programming.md`).
> Marked 🔁 throughout. Re-verify against the original DataCamp screens when possible.

### 3.7 Class attributes + the shadowing gotcha  🔁
**Taught.** Data shared across all instances, defined in the class body (`MIN_SALARY`,
`MAX_POSITION`), referenced as `ClassName.ATTR`.
**Gotcha (I flagged this myself).** `p1.MAX_SPEED = 7` does **not** change the class attribute —
it silently creates a *new instance attribute* shadowing it, leaving `p2` and the class untouched.
To change it for all, assign `Player.MAX_SPEED = 7`.
**Reproduce cold.** Explain instance-assignment shadowing vs mutating the class attribute.

### 3.8 `@classmethod` — alternative constructors  🔁
**Taught / Practiced.** `@classmethod` with `cls`; a class has one `__init__` but classmethods can
build instances other ways and `return cls(...)`. Practiced `BetterDate.from_str("2020-04-30")`
(split + `map(int, ...)`) and `from_datetime(dt)`.
**Reproduce cold.** Write a `from_str` classmethod returning `cls(...)`.

### 3.9 Inheritance + `super()`  🔁
**Taught / Practiced.** `class Manager(Employee)` inherits everything, then customizes. Call the
parent constructor via `Employee.__init__(self, ...)` **or** `super().__init__(...)`. Override +
extend by calling the parent inside the child (`Manager.give_raise` computes a bonus then defers
to `Employee.give_raise`).
**Portfolio-critical (I flagged this as the payoff).** `class MyModel(BaseModel)` **is inheritance**
— declaring fields inherits Pydantic's validation machinery. Proven by subclassing a real
`pd.DataFrame` (`LoggedDF`): `super().__init__(*args, **kwargs)` forwards every argument, plus an
overridden `to_csv` — the `*args/**kwargs` passthrough from Course 1.
**Reproduce cold.** Write `Sub(Parent)` calling `super().__init__(*args, **kwargs)`; explain why
`class MyModel(BaseModel)` gives validation for free.

### 3.10 Operator overloading — `__eq__` (Ch 3 start)  🔁
**Taught.** By default `==` compares object identity (references), so two objects with identical
data are "not equal." Override `__eq__(self, other)` to compare by *data*.
**Reproduce cold.** Explain identity vs data equality; when to override `__eq__`.

### 3.11 Ch 4 — Liskov, internal attributes, `@property`  🔁
**Taught / Practiced.**
- **Liskov Substitution.** A subclass must be safely substitutable for its parent. The
  Rectangle/Square trap: `Square(Rectangle)` *looks* like clean inheritance but violates LSP,
  because a Square can't vary height/width independently; fix by overriding both setters to keep
  sides equal. Lesson: **"is-a" inheritance can be wrong even when it feels natural.**
- **Internal attributes.** The single-underscore `_name` convention signals "internal."
- **`@property`.** A getter (`@property`) + `@x.setter` gives controlled attribute access with
  validation on *every* assignment — e.g. a `Customer` balance backed by `_balance`, rejecting
  negatives, so `cust.balance = 3000` validates automatically. Getter with no setter =
  **read-only** attribute. This extends Ch 1's constructor validation to every write.
**Mistakes & debugging (I recorded these myself).** `self.h = set_h` (assigned the *function
name* instead of the parameter); an extra parameter that broke the method interface (`TypeError`);
an indentation mismatch.
**Portfolio link.** `@property` + setter = how to enforce invariants on financial fields
(never-negative balances) in PREPARE/LUCENT — validate on every write, not just at creation.
**Reproduce cold.** Write a `@property`/setter pair with validation; explain the Liskov
Rectangle/Square trap.

---

# Course 4 — Introduction to Testing in Python

## Chapter 1  🔁 Recovered from another provided study record

> **Not in the two DataCamp files.** Reconstructed from my *own* pytest practice screenshots
> uploaded earlier in this chat (captured in `datacamp/courses/04-introduction-to-testing.md`).
> Marked 🔁. Re-verify against the original DataCamp screens.

### 4.1 Assert-based tests  🔁
**Taught / Practiced.** A test is a `test_*` function of `assert`s; passes if every assert holds.
```python
def test_numbers():
    assert multiple_of_two(2) is True
    assert multiple_of_two(4) is False
```
**Reproduce cold.** Write an assert-based test against specified behavior.

### 4.2 Exception testing — `pytest.raises`  🔁
**Taught / Practiced.**
```python
def test_zero():
    with pytest.raises(ValueError):
        multiple_of_two(num=0)     # passes iff this raises ValueError
```
The block asserts the code inside *does* raise; the test fails if it does **not**.
**Reproduce cold.** Test that bad input raises, using the `pytest.raises` context manager.

### 4.3 Markers  🔁
**Taught.** `@pytest.mark.skip` (skip indefinitely) · `@pytest.mark.skipif(condition)` (skip only
if condition True — e.g. Python version, or a condition string like `'day_of_week == 6'` with
`datetime`) · `@pytest.mark.xfail` (expected to fail).
**Reproduce cold.** Match each marker to its use; write a conditional skip.

### 4.4 Running from the CLI  🔁
**Taught / Practiced.** `pytest run_the_test.py` → "2 passed" (`..`); `pytest run_the_test.py -k
"numbers"` → keyword selection → "1 passed, 1 deselected." Meaningful test names matter.
**Reproduce cold.** Run a suite and a keyword-filtered subset from the CLI.

### 4.5 Fluency notes I caught (from my own review)  🔁
- `list(set(x)) == [1,2,3]` is **fragile** — sets are unordered; use `sorted(...)`.
- `is True` is an *identity* check (stricter than truthiness); works here only because the value
  is a real bool.
- `raise(ValueError)` → idiomatic `raise ValueError("message")`.
**Interview link.** This is the concrete answer to "how do you verify AI-generated code?" —
"pytest asserts against specified behavior + `pytest.raises` for exceptions," which promoted that
line out of the reinforcement log's "Not yet" bank.

## Chapters 2–4  ❌ Missing from available materials

**Not documented anywhere I've provided.** Expected topics (named as "still unclear / later
chapters" in my own Course-4 write-up, **not** as completed content): **fixtures**
(`@pytest.fixture`, setup/teardown), **parametrization** (`@pytest.mark.parametrize`), and
**mocking** (isolating a unit from its dependencies). These are what deepen "I write asserts" into
"I test units in isolation." **Do not treat as learned.** Recover from the original DataCamp course
when I resume it, then fill this section.

---

# Knowledge-gap register (for future review sessions)

Separated by confidence, per the four-way legend.

### ✅ Confirmed from source notes — solid, drill for retention
Course 1 (all 6 topics + recall questions) · Course 2 (all 12 topics + recall questions) ·
OOP Chapter 1 (exploration, class/object, attributes/methods, `self`, `__init__`+validation, Point).

### 🔁 Recovered from another provided study record — verify against original
OOP Ch 2 (class attributes, shadowing, `@classmethod`, inheritance, `super()`) · OOP Ch 3 (`__eq__`) ·
OOP Ch 4 (Liskov, `_internal`, `@property`) · Testing Ch 1 (assert, `pytest.raises`, markers, CLI).
*Reconstructed from my own earlier uploads in this chat, not from the two DataCamp files.*

### 🕳️ Needs active review / cold recall — documented, but re-derive unaided
- Course 1 recall questions (function/method, module/package, `*args`/`**kwargs`, `try/except` vs `raise`).
- Course 2: write the **merge step with correct pointer advancement** cold; explain each sort's
  strategy; the "recursion must shrink" principle in my own words.
- Iterative + recursive **binary search** from scratch.
- Recursive + memoized **Fibonacci** from scratch.
- OOP: `@property`/setter with validation; the Liskov Rectangle/Square explanation.

### ❌ Missing from available materials — cannot reconstruct honestly
- **Testing Chapters 2–4**: fixtures, parametrization, mocking. Not in any provided material.
  Recover from the original DataCamp course; until then, not claimable as learned.
- Any Course-4 content beyond Chapter 1.

---

## How to use this document

1. **Retention pass:** read a ✅ section, then answer its "Reproduce cold" line without scrolling up.
2. **Recovery verification:** when back in DataCamp, confirm the 🔁 sections against the real
   course screens and upgrade them to ✅ (or correct them).
3. **Fill the ❌ gap:** finish Testing Ch 2–4, then write those sections from the original course.
4. **Interview prep:** the "Connects to" lines are the bridges from a course fact to a portfolio
   or job-prep answer — rehearse those, not the raw syntax.
