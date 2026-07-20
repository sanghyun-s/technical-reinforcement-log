# Data Structures and Algorithms in Python

| | |
|---|---|
| **Priority** | Tier A · #1 (roadmap) |
| **Status** | In progress — linked lists, stacks, queues, Big O covered |
| **Started** | 2026-07-19 |
| **Completed** | — |
| **Link** | https://app.datacamp.com/learn/courses/data-structures-and-algorithms-in-python |

---

## What this defends

**The gap it closes:**
The data structures *underneath* the LeetCode patterns. Before this, every stack/queue
problem cost double — learning the pattern and the structure at the same time. This pays
that debt down once. Covers 3 of the 8 §5.2 categories directly (stack/queue) and underpins
a fourth (the hash-map problems lean on the same "what does this structure cost" thinking).

**The app it connects to:** General fluency, not a specific app. This is capability, not
positioning.

**The Tier 2 question it moves toward Tial 1:**
> "What's the time complexity of your approach, and why?"
Big O is now vocabulary I own rather than repeat.

---

## Concepts I could not have explained before this course

1. **A linked list stores data without contiguous memory.** Each node holds its value plus a
   pointer (`next`) to the following node. That's why insertion at the front is cheap — you
   just repoint `head` — where the same operation on an array shifts every element.

2. **The `Node` / `__init__` pattern.** Every node is an object with two attributes: `value`
   (the data) and `next` (initially `None`). This is my first real contact with classes
   outside of "Pydantic is a class" — and it's the OOP bridge to roadmap #2.

3. **Stack = LIFO, and push/pop are both O(1).** `push` creates a node, points its `next` at
   the current top, then moves `top` to the new node. `pop` reads the top, moves `top` to
   `top.next`, and returns the value. No shifting, no scanning — that's why the operations
   are constant time.

4. **The empty-stack guard.** `pop` checks `if self.top is None: return None` before touching
   anything. Skipping that guard is an `AttributeError` waiting to happen — the same
   "fail at the boundary" idea from the Intermediate Python error-handling chapter.

5. **Big O measures the worst case** — time (how long to run) and space (extra memory). O(1)
   constant, O(n) linear, and why a single `for` over a list is O(n) while a linked-list
   head insert is O(1).

6. **Python's `queue.LifoQueue`** exists as a built-in stack, so I don't hand-roll one in
   practice — but building it by hand once is what makes the built-in legible.

---

## Direct hits on my own codebase

| Course concept | Where it shows up | What I now understand |
|---|---|---|
| Big O / worst case | Every "what's the complexity" interview question | I can now name *why* an approach is O(n) vs O(n²), not just assert it |
| Class / `__init__` / `self` | Pydantic models, FastAPI structure | First real grasp of what instantiation does — bridges to roadmap #2 (OOP) |
| Stack LIFO | Undo/history-style logic | The mental model for any "last thing first" operation |

---

## Still unclear

Internal note — open-ended, closed by later courses.

- Queue (FIFO) internals — covered lightly; want to rebuild `enqueue`/`dequeue` from scratch.
- When a linked list beats a Python `list` in real code (vs. just in interview theory).
- How `self` actually binds — clicks a bit, but roadmap #2 (OOP) is where this closes.

---

## Interview sentence

> I started the DSA course to close the gap under my LeetCode practice — I was learning
> patterns without understanding the structures beneath them. Building a stack by hand, with
> O(1) push and pop and an empty-stack guard, is what made Big O concrete instead of
> memorized.

---

## Follow-up keywords

`linked list` · `LIFO / FIFO` · `Big O worst case` · `queue.LifoQueue` · `Node class` ·
`self / instantiation`
