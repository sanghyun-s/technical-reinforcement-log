# Tier A — Learning Materials Synthesis (Study Reference)

**Internal note — study-oriented.** The consolidated technical content + practice code from the
Python fluency block, in one place to **re-read and keep fresh**. This is the *code-forward*
companion to [TIER-A-INTERVIEW-COMPILATION.md](./TIER-A-INTERVIEW-COMPILATION.md) — that note is
what I *say* in an interview; this note is what I *study from*.

**Covers:** [01 Intermediate Python](./courses/01-intermediate-python-for-developers.md) ·
[02 DSA](./courses/02-data-structures-and-algorithms.md) ·
[03 OOP](./courses/03-object-oriented-programming.md).
**Pending:** Testing in Python (folds in when notes are written up).

---

## 01 — Intermediate Python: code patterns

**Custom function — default arg + docstring + typed guard**
```python
def calculate_discount(price, discount_percent=15, round_result=True):
    """Calculate the discounted price of a product."""
    discounted = price - price * (discount_percent / 100)
    return round(discounted, 2) if round_result else discounted
```

**Arbitrary args — `*args` (tuple) vs `**kwargs` (dict)**
```python
def concat(*args):              # args is a TUPLE
    return " ".join(args)
def concat_kw(**kwargs):        # kwargs is a DICT — iterate .values()
    return " ".join(kwargs.values())
```

**lambda + map (lazy — wrap in list to materialise)**
```python
cleaned = list(map(lambda s: s.replace(" ", "_").lower(), names))
```

**Error handling — swallow vs raise**
```python
try:
    return text.replace(" ", "_").lower()
except AttributeError:                     # name the exception, not bare except
    print("expected a string")
# OR fail loud at the boundary:
if not isinstance(text, str):
    raise TypeError("clean_text expects a string")
```

---

## 02 — Data Structures & Algorithms: code patterns

**Stack (LIFO) — O(1) push/pop, guard empty**
```python
def push(self, data):
    self.top = Node(data, self.top); self.size += 1
def pop(self):
    if self.top is None: return None          # empty-stack guard
    val, self.top = self.top.data, self.top.next
    self.size -= 1; return val
```

**Queue (FIFO) — O(1) enqueue/dequeue with head+tail**
```python
def enqueue(self, data):
    node = Node(data)
    if self.head is None: self.head = self.tail = node
    else: self.tail.next = node; self.tail = node
```

**Binary search — iterative & recursive (O(log n))**
```python
def binary_search(a, x):
    lo, hi = 0, len(a) - 1
    while lo <= hi:
        mid = lo + (hi - lo) // 2            # overflow-safe midpoint
        if a[mid] == x: return True
        if x < a[mid]: hi = mid - 1
        else:          lo = mid + 1
    return False

def bsearch_rec(a, x):
    if not a: return False
    mid = len(a) // 2
    if a[mid] == x: return True
    return bsearch_rec(a[:mid], x) if x < a[mid] else bsearch_rec(a[mid+1:], x)
```

**Recursion + memoization (exponential → linear)**
```python
def fib(n):
    if n <= 1: return n
    return fib(n-1) + fib(n-2)                # NOT n * fib(n-1) (that's factorial!)

cache = [None] * 100
def fib_memo(n):
    if n <= 1: return n
    if cache[n] is None: cache[n] = fib_memo(n-1) + fib_memo(n-2)
    return cache[n]
```

**BFS (queue + visited)**
```python
def bfs(graph, start, target):
    seen, q = [start], deque([start])
    while q:
        v = q.popleft()
        if v == target: return True
        for nb in graph[v]:
            if nb not in seen: seen.append(nb); q.append(nb)
    return False
```

**Sort skeletons (Ch 4)** — merge = divide & conquer O(n log n); quicksort = two-pointer
partition around a pivot, avg O(n log n). *Bug watch:* each merge "drain" loop advances **its
own** pointer; quicksort recurses on **sub-ranges**, not the whole list.

**Big O quick table**

| Structure / op | Time | Note |
|---|---|---|
| Linked-list head insert | O(1) | no shifting |
| Stack push/pop, Queue enq/deq | O(1) | with right pointers |
| Binary search | O(log n) | sorted input |
| Merge / quicksort | O(n log n) | quicksort O(n²) worst |
| Bubble/selection/insertion | O(n²) | |

---

## 03 — Object-Oriented Programming: code patterns

**Class + constructor validation**
```python
class Employee:
    def __init__(self, name, salary=0):
        self.name = name
        self.salary = salary if salary > 0 else 0    # validate at creation
    def give_raise(self, amount): self.salary += amount   # mutates
    def monthly_salary(self):    return self.salary / 12  # returns
```

**Inheritance + `super()` + override-and-extend**
```python
class Manager(Employee):
    def give_raise(self, amount, bonus=1.05):
        super().give_raise(amount * bonus)           # reuse parent, add the twist
```

**`@classmethod` — alternative constructor**
```python
    @classmethod
    def from_str(cls, s):
        y, m, d = map(int, s.split("-"))
        return cls(y, m, d)                           # returns a new instance
```

**`@property` — validated + read-only attributes**
```python
class Customer:
    def __init__(self, name, bal):
        self.name = name
        if bal < 0: raise ValueError("Invalid balance!")
        self._balance = bal
    @property
    def balance(self):            return self._balance     # getter
    @balance.setter
    def balance(self, new):                                # runs on cust.balance = X
        if new < 0: raise ValueError("Invalid balance!")
        self._balance = new
# getter with NO setter  => read-only attribute
```

**Subclassing a real library class (`*args/**kwargs` passthrough)**
```python
class LoggedDF(pd.DataFrame):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)             # forward every DataFrame arg
        self._created_at = datetime.today()
```

**Liskov trap — Square isn't safely a Rectangle**
```python
class Square(Rectangle):
    def set_h(self, h): self.h = self.w = h           # override both so sides stay equal
    def set_w(self, w): self.w = self.h = w           # or substitution breaks callers
```

---

## Consolidated idioms & gotchas (all three courses)

| Write this | Not this | Why |
|---|---|---|
| `if x:` / `if not x:` | `if x == True:` / `if x == False:` | booleans are already truthy |
| `nums[:-1]`, `nums[::-1]` | `nums[0:len(nums)-1]` | clearer; slice returns a new list |
| a new var name | `list = [...]` | shadows the built-in `list` |
| `abs(x)`, `Counter(s)` | `abs[x]`, `Counter[s]` | `()` calls, `[]` indexes |
| `{k: v for ...}` | `{k == v for ...}` | colon = dict; no colon = **set** of booleans |
| `Player.MAX = 7` | `p1.MAX = 7` (to change for all) | instance assignment **shadows**, never mutates the class |

**Mutation reference (the through-line):**

| Op | New object? | Mutates? | Returns |
|---|---|---|---|
| `sorted(x)` / `x[::-1]` / `reversed(x)` | yes / yes / lazy | no | list / list / iterator |
| `x.sort()` / `x.reverse()` | no | **yes** | `None` |
| `a + b` | yes | no | new list |
| `a.extend(b)` / `a.append(v)` | no | **yes** | `None` |

---

## Practice-exercise index (re-drill fodder)

Rebuild these from a blank page to test retention:
- **01:** `calculate_discount` (defaults + docstring) · `concat(*args)` / `(**kwargs)` · lambda-map name cleaner · `clean_text` try/except + raise.
- **02:** stack push/pop · queue enqueue/dequeue · binary search (both) · Fibonacci + memo · BFS · `TreeNode` + BST insert · weighted graph.
- **03:** `Employee` + `give_raise` · `Point` (`distance_to_origin`, `reflect`) · `Manager(Employee)` + super · `BetterDate.from_str` · `LoggedDF(pd.DataFrame)` · `Customer` `@property` · `Rectangle`/`Square` (Liskov).

---

## Pending

**Testing in Python** — when notes land, add its section here (pytest structure, `assert`
patterns, fixtures, `unittest` basics) and its practice-exercise index, mirroring the format above.
