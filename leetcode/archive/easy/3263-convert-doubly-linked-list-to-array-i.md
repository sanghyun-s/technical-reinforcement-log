# 3263. Convert Doubly Linked List to Array I

**Difficulty:** Easy
**Pattern:** Linked List / Traversal / List Construction
**Date Solved:** 2026-06-10
**Status:** ✅

🎉 **First Linked List problem on the log!** This unlocks the entire LL family — traversal, reversal, cycle detection, merging, two-pointer techniques. The basic "walk-and-collect" template you write today is the foundation for all of it.

---

## Understanding the Goal

Walk through a doubly linked list and collect values into an array, in order.

**The "doubly" is a red herring here.** A doubly linked list has BOTH `next` and `prev` pointers, but for forward traversal we only need `next`. The `prev` pointer matters when you need to traverse backward or modify the list in both directions.

**The canonical linked list traversal template:**

```python
curr = head
while curr:
    # do something with curr.val
    curr = curr.next
```

This is **the most fundamental linked list pattern.** Internalize it now.

---

## LAYER 1: Line-by-Line Explanation

### Pythonic — while-loop traversal (THE template)

```python
class Solution:
    def toArray(self, root: 'Optional[Node]') -> List[int]:
        # Walker pointer — start at the head of the list
        # Note: problem names parameter 'root' (unusual; usually 'head')
        curr = root
        
        # Result accumulator — append values as we walk
        result = []
        
        # Loop continues while curr is not None
        # When curr becomes None, we've walked past the last node
        # Python treats None as falsy, so `while curr:` is idiomatic
        while curr:
            result.append(curr.val)        # Collect this node's value
            curr = curr.next                # Advance to next node
        
        return result
```

**Critical detail — `while curr:` vs `while curr is not None:`:**

Both work. `while curr:` relies on `None` being falsy. Some style guides prefer the explicit `while curr is not None:`. **Both are idiomatic Python** — pick one and be consistent.

**Why `prev` pointer is irrelevant:**

The doubly linked list gives you `prev` for free, but for forward traversal we only read `next`. We're not asked to reverse, not asked to modify — just collect values in order. **The "doubly" part is a distractor.**

### List comprehension with itertools

```python
class Solution:
    def toArray(self, root):
        def walk(node):
            while node:
                yield node.val
                node = node.next
        return list(walk(root))
```

Slightly more functional. Generator + `list()` constructor.

### Recursive (elegant but stack-heavy)

```python
class Solution:
    def toArray(self, root):
        if root is None:
            return []
        return [root.val] + self.toArray(root.next)
```

**Why this is risky for large lists:**

Each recursive call adds a frame to Python's call stack. Python's default recursion limit is **~1000**. For n=50 (problem constraints), this is fine. For longer lists, you'd hit `RecursionError`.

**Why list concatenation here is O(n²):**

Each `[root.val] + recursive_result` creates a NEW list. For n elements: O(n) + O(n-1) + ... + O(1) = O(n²). The iterative version is O(n).

### Compact while with collect-then-advance

```python
class Solution:
    def toArray(self, root):
        result, curr = [], root
        while curr:
            result.append(curr.val)
            curr = curr.next
        return result
```

Same as canonical, just initializes on one line.

---

## LAYER 2: Worked Examples

### Example 1: `head = [1, 2, 3, 4, 3, 2, 1]` → `[1, 2, 3, 4, 3, 2, 1]`

```
head → [1] ⇄ [2] ⇄ [3] ⇄ [4] ⇄ [3] ⇄ [2] ⇄ [1] → None
```

Trace:
| step | curr.val | result after | curr after |
|------|----------|--------------|------------|
| 1 | 1 | [1] | node(2) |
| 2 | 2 | [1, 2] | node(3) |
| 3 | 3 | [1, 2, 3] | node(4) |
| 4 | 4 | [1, 2, 3, 4] | node(3) |
| 5 | 3 | [1, 2, 3, 4, 3] | node(2) |
| 6 | 2 | [1, 2, 3, 4, 3, 2] | node(1) |
| 7 | 1 | [1, 2, 3, 4, 3, 2, 1] | None |

Loop exits. Return `[1, 2, 3, 4, 3, 2, 1]` ✓

### Example 2: `head = [2, 2, 2, 2, 2]` → `[2, 2, 2, 2, 2]`

Duplicates fine — we just append each `curr.val`. 5 iterations → 5 twos. ✓

### Example 3: Single-node list `head = [5]` → `[5]`

Iteration 1: `curr.val = 5`, `result = [5]`, `curr = None`. Return `[5]` ✓

### Edge cases

- **Single node:** Loop runs once → `[head.val]`
- **head is None** (defensive): Loop never enters → `[]`. Code handles implicitly.
- **All same values:** No deduplication; we collect every visit
- **Max n=50:** Instant — 50 iterations

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **While-loop iterative** | **O(n)** | **O(n) output** | **Standard** |
| Recursive | O(n²) | O(n) stack | List concat is O(n) per step |
| Generator + list() | O(n) | O(n) | Slightly slower constant |

**The linked list traversal template (MEMORIZE):**

```python
curr = head
while curr:
    # PROCESS curr
    curr = curr.next
```

This skeleton powers:
- Read all values (this problem)
- Find a value
- Compute length (`len += 1` instead of append)
- Sum / max / min
- Check property (`if condition: return ...`)
- Modify values in-place (`curr.val = new_val`)

**Three pointer variables you'll use constantly:**

| name | meaning |
|------|---------|
| `head` | The first node (passed in; don't reassign) |
| `curr` | The current walker |
| `prev` | Previous node (for reversal or removal) |
| `dummy` | Sentinel node to simplify head-edge cases |

**Why don't we reassign `head`?**

Treat `head` as read-only. Use a separate `curr` walker. If you reassign `head`, you lose access to the start of the list — and can't return to it. **Always walk with a separate pointer.**

**Connection to tree DFS:**

You've drilled tree DFS extensively (LC 94, 100, 104, 226, 1382). Linked lists are essentially **trees with only one child** — every node has at most one `next`. Tree traversal generalizes; LL traversal is the base case.

```python
# Tree DFS                          # LL traversal
def dfs(node):                      curr = head
    if not node: return             while curr:
    process(node)                       process(curr)
    dfs(node.left)                      curr = curr.next
    dfs(node.right)
```

LL is "linear tree DFS" — same skeleton without branching.

**Why doubly linked here is a no-op:**

Doubly linked lists matter when you need:
1. **Backward traversal** (`curr = curr.prev`)
2. **O(1) deletion given a pointer** (no need to track prev)
3. **Bidirectional iteration**

This problem just asks for forward order — singly linked would suffice.

**A trap to avoid — `head.next` vs `head = head.next`:**

```python
# WRONG — reading next.val but never advancing
while head:
    result.append(head.next.val)   # Will crash when head.next is None!
    head = head.next.next          # Skipping every other node!
```

Always read the CURRENT node's value, THEN advance.

**The "two phases" mental model:**

For every LL problem, separate two concerns:
1. **What to DO at each node** (process, count, modify)
2. **How to ADVANCE** (`curr = curr.next`)

Conflating these leads to bugs. Keep them on separate lines.

---

## LAYER 4: Interview Variations

• **Reverse order:** Traverse using `prev` pointer starting from tail; or traverse forward and `result.reverse()` at end.
• **Find the tail:** Walk until `curr.next is None`; return `curr`.
• **Compute length:** `count = 0; while curr: count += 1; curr = curr.next`.
• **Sum of values:** `total = 0; while curr: total += curr.val; curr = curr.next`.
• **Check if value exists (LC 1290):** Loop with early return on match.
• **Singly linked to array:** Same code; `prev` doesn't matter.
• **Array to doubly linked:** Build nodes, link both `next` and `prev`.
• **Middle of linked list (LC 876):** Slow + fast (Floyd's tortoise and hare).
• **Reverse linked list (LC 206):** Three pointers: `prev`, `curr`, `next_temp`.
• **Detect cycle (LC 141):** Floyd's cycle detection.
• **Merge two sorted lists (LC 21):** Two pointers, build with dummy.
• **Reverse doubly linked list:** Swap `prev` and `next` at every node.
• **Find k-th node from end:** Two pointers k apart.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — canonical while-loop traversal
class Solution:
    def toArray(self, root: 'Optional[Node]') -> List[int]:
        curr = root
        result = []
        while curr:
            result.append(curr.val)
            curr = curr.next
        return result
```

**Compact one-line init:**
```python
def toArray(self, root):
    result, curr = [], root
    while curr:
        result.append(curr.val)
        curr = curr.next
    return result
```

**Generator-based:**
```python
def toArray(self, root):
    def walk(node):
        while node:
            yield node.val
            node = node.next
    return list(walk(root))
```

**Recursive (avoid for large n):**
```python
def toArray(self, root):
    if root is None:
        return []
    return [root.val] + self.toArray(root.next)
```

**Tail-recursive with accumulator:**
```python
def toArray(self, root):
    def helper(node, acc):
        if node is None:
            return acc
        acc.append(node.val)
        return helper(node.next, acc)
    return helper(root, [])
```

---

**Time:** O(n) | **Space:** O(n) for output (O(1) auxiliary excluding output)

**Pattern flag:** "Walk through a linked list and DO SOMETHING per node" → **the while-curr template**:

```python
curr = head
while curr:
    # process curr
    curr = curr.next
```

**This is THE foundational linked list pattern.** Every LL problem builds on it. The "doubly linked" framing is a distractor — forward traversal needs only `next`. Use `prev` only when traversing backward or modifying both directions.

LL traversal is **"linear tree DFS"** — same shape as tree DFS without branching. You already know the harder version (Day 9: LC 94, 100, 104, 226).

🎉 **First Linked List problem on the log!** Foundation for the entire LL family.
