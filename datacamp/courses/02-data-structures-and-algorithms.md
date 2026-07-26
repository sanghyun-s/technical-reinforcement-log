# Data Structures and Algorithms in Python

| | |
|---|---|
| **Priority** | Tier A · #1 (roadmap) |
| **Status** | In progress — Chapters 1–4 covered (queues, trees, graphs, recursion, binary search, BFS) |
| **Started** | 2026-07-19 |
| **Last worked** | 2026-07-25 |
| **Link** | https://app.datacamp.com/learn/courses/data-structures-and-algorithms-in-python |

---

## What this defends

The data structures *underneath* the LeetCode patterns. Covers stack/queue, trees (DFS/BFS),
graphs, recursion, and binary search directly — five of the eight §5.2 families, plus the
Big O vocabulary to explain any of them. This is capability, not positioning: it changes what
I can build and analyze, not just what I can say about the portfolio.

---

## Concepts covered (Ch 2–4)

**Queues**
- Implemented `PrinterTasks` on top of a `Queue` class — `add_document` enqueues,
  `print_documents` drains while `has_elements()`.
- `enqueue`/`dequeue` on a singly linked list with head+tail pointers are both **O(1)** —
  no shifting, unlike a list-backed queue. Confirmed the Big O myself.
- Python built-in: `queue.SimpleQueue()` — `.put()` / `.get()`.

**Trees**
- `TreeNode(data, left, right)` — the fix was storing `left`/`right` into
  `self.left_child`/`self.right_child`, and passing the child nodes when building the root.
- BST insert relies on ordering (alphabetical here) to place each node left/right.

**Graphs**
- Adjacency via a dict: `{vertex: [neighbors]}`. For a **weighted** graph, store
  `[target, weight]` pairs instead of bare targets.
- **BFS** with `queue.SimpleQueue` + a `visited` list: dequeue current, check match, enqueue
  unvisited neighbors. FIFO is what makes it breadth-first.

**Recursion**
- **Fibonacci** — base case `if n <= 1: return n`, then `fibonacci(n-1) + fibonacci(n-2)`.
  ⚠ Caught my own slip: I first wrote `n * fibonacci(n-1)`, which is *factorial*. The
  memoized version (`cache[n] = fibonacci(n-1) + fibonacci(n-2)`) was correct — so the plain
  version was a typo, not a concept gap, but it would fail a trace.
- **Memoization** — a `cache` list turns exponential naive Fibonacci into linear by storing
  solved subproblems. First real contact with dynamic-programming thinking.
- **Towers of Hanoi** — recursive: move n-1 aside, move disk n, move n-1 back. Base guard
  should be `if num_disks >= 1:` (double-check: `>= 0` prints a spurious disk-0 move).

**Binary search**
- Iterative: `first`/`last` pointers, `middle = (first+last)//2`, move the bound that can't
  contain the target. O(log n).
- Recursive: base case empty list → False; else recurse on `list[:middle]` or
  `list[middle+1:]`. Elegant, but slicing copies the list each call, so it costs extra space
  the iterative version doesn't.

---

## Direct hits on my own codebase

| Concept | Where it shows up | What I now understand |
|---|---|---|
| Big O worst case | Every "complexity?" interview question | Can justify O(1) / O(log n) / O(n), not just assert |
| Adjacency dict | Any graph-shaped data | The `{key: [neighbors]}` shape is just a dict of lists |
| Memoization | Any repeated-subproblem computation | Why caching collapses exponential work to linear |
| Recursion base case | PDF/agent recursion in PREPARE | A missing/wrong base case is how you get infinite recursion |

---

## Still unclear (open — internal note)

- BST *delete* (insert + search are solid; delete's three cases aren't yet).
- DFS iterative with an explicit stack — recursive DFS is clear, the stack version less so.
- When recursion's clarity is worth its call-stack cost vs. an iterative rewrite.

---

## Interview sentence

> I worked through the DSA course to close the gap under my LeetCode practice — building
> queues, a BST, a weighted graph, and both binary searches by hand. The payoff is that Big O
> is now something I can justify from the data structure up, not memorize, and I can explain
> why an O(1) linked-list enqueue beats a list-backed one.

---

## Follow-up keywords

`BFS / FIFO` · `adjacency list` · `memoization` · `recursion base case` · `binary search O(log n)` · `BST operations`
