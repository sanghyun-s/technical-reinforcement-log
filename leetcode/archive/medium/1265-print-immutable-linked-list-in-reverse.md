# 1265. Print Immutable Linked List in Reverse

**Difficulty:** Medium
**Pattern:** Linked List / Recursion / Stack / Time-Space Tradeoff (sqrt decomposition)
**Date Solved:** 2026-06-10
**Status:** ✅

🎯 **First Medium Linked List problem on the log.** The basic solution is trivial (3 lines of recursion), but the **follow-up question is a gem** — it introduces **sqrt(n) decomposition**, a classic time-space tradeoff that shows up in segment trees, Mo's algorithm, and other advanced techniques.

---

## Understanding the Goal

Print a linked list's values **in reverse order**, but you can ONLY use two API methods:
- `head.printValue()` — print current node's value
- `head.getNext()` — get the next node

**The constraints make this interesting:**
1. **Can't modify the list** (immutable — no reversing)
2. **Can't access `.val` directly** (must call `printValue()`)
3. **Can't go backwards** (no `prev` pointer)
4. **Follow-up:** can we use less than O(n) extra space?

**Why this is Medium:**

The basic recursive solution is 3 lines. But the **follow-up** — achieving sub-linear space — is where the real algorithm work happens. The interview signal is whether you can recognize and implement the **sqrt(n) decomposition technique**.

---

## LAYER 1: Line-by-Line Explanation

### Approach 1: Recursion (Editorial) — O(n) time, O(n) space

```python
class Solution:
    def printLinkedListInReverse(self, head: 'ImmutableListNode') -> None:
        # Base case: walked past the end, stop recursing
        # The "fall off the cliff" termination
        if head is not None:
            # Step 1: RECURSE FIRST — go all the way to the end
            # Stacks up calls: print(node1) → print(node2) → ... → print(last) → None
            self.printLinkedListInReverse(head.getNext())
            
            # Step 2: PRINT AFTER recursion returns
            # Deepest call (last node) finishes first
            # As stack unwinds, we print from last back to first
            head.printValue()
```

**Why this prints in reverse — the unwinding insight:**

Think of recursion as a stack of "I'll do this AFTER my recursive call comes back":

```
Call printRev(node1)
  → recurse into printRev(node2)         ← stack frame 2 paused
    → recurse into printRev(node3)       ← stack frame 3 paused
      → recurse into printRev(node4)     ← stack frame 4 paused
        → recurse into printRev(None)
        → returns immediately
      → NOW print node4.val              ← unwinding starts
    → NOW print node3.val
  → NOW print node2.val
→ NOW print node1.val
```

**Recursive call goes forward, prints happen on the way back.** This is the **"recurse first, act after"** pattern — same shape as post-order tree traversal.

### Approach 2: Iterative with explicit stack — O(n) time, O(n) space

```python
class Solution:
    def printLinkedListInReverse(self, head: 'ImmutableListNode') -> None:
        # Build a stack of nodes by walking forward
        stack = []
        curr = head
        while curr is not None:
            stack.append(curr)              # Push each node
            curr = curr.getNext()
        
        # Pop in LIFO order — last pushed is first printed
        # This gives reverse order without recursion
        while stack:
            stack.pop().printValue()        # Pop and immediately print
```

**Why this is equivalent to recursion:**

Recursion uses Python's call stack implicitly. Here we use an explicit list as a stack. Same algorithmic content, different mechanics.

| Recursion | Explicit stack |
|-----------|----------------|
| 3 lines, cleanest | More code but flatter |
| Risk: stack overflow for huge lists | No recursion limit |
| Hidden space cost | Visible space cost |

For n=1000, both work. For n=10⁶, the recursive version might hit Python's default recursion limit (~1000). The explicit stack is **safer at scale**.

### Approach 3: Brute force O(1) space — O(n²) time, O(1) space

```python
class Solution:
    def printLinkedListInReverse(self, head: 'ImmutableListNode') -> None:
        # First find the length of the list
        n = 0
        curr = head
        while curr is not None:
            n += 1
            curr = curr.getNext()
        
        # For each position i from n-1 down to 0, walk i steps from head and print
        # Total work: n + (n-1) + ... + 1 = O(n²)
        for i in range(n - 1, -1, -1):
            curr = head
            for _ in range(i):
                curr = curr.getNext()
            curr.printValue()
```

**The brute-force time-space tradeoff:**

By using only a counter and pointer, we achieve **O(1) extra space** — but pay O(n²) time. For n=1000, that's 10⁶ ops — still fast in practice.

Demonstrates that the recursion's O(n) space wasn't strictly necessary. We can trade time for space.

### Approach 4: The Gem — sqrt(n) decomposition ⭐

**O(n) time, O(sqrt(n)) space.** This is the **follow-up answer that earns the Medium difficulty.**

```python
import math

class Solution:
    def printLinkedListInReverse(self, head: 'ImmutableListNode') -> None:
        # Step 1: count length of list
        n = 0
        curr = head
        while curr is not None:
            n += 1
            curr = curr.getNext()
        
        # Step 2: compute chunk size — sqrt(n) rounded up
        k = max(1, int(math.sqrt(n)))
        
        # Step 3: walk again, saving checkpoint nodes
        # Result: ~sqrt(n) checkpoints spaced k apart
        checkpoints = []
        curr = head
        for i in range(n):
            if i % k == 0:
                checkpoints.append(curr)
            curr = curr.getNext()
        
        # Step 4: process chunks from last to first
        # For each chunk (in reverse), recursively print it in reverse
        # Each chunk has ≤ k nodes → recursion depth ≤ k = sqrt(n)
        def print_chunk(node, end):
            if node is end:
                return
            print_chunk(node.getNext(), end)        # recurse first
            node.printValue()                         # print on way back
        
        # Walk checkpoints from last to first
        for i in range(len(checkpoints) - 1, -1, -1):
            end = checkpoints[i + 1] if i + 1 < len(checkpoints) else None
            print_chunk(checkpoints[i], end)
```

**The "checkpoint" idea — pictorially:**

```
Original list (n=16, k=4):
node[0] → node[1] → node[2] → node[3] → node[4] → ... → node[15] → None

Checkpoints saved: [node[0], node[4], node[8], node[12]]   ← only 4 = sqrt(16) pointers
                    ↑          ↑          ↑          ↑
                    chunk A    chunk B    chunk C    chunk D

To print reverse: print chunk D reversed, chunk C reversed, B reversed, A reversed.
Each chunk recursed → recursion depth ≤ k = sqrt(n).
```

**Why this achieves O(sqrt(n)) space:**

1. **Checkpoints array:** holds ~sqrt(n) node references → O(sqrt(n))
2. **Recursion depth per chunk:** at most k = sqrt(n) frames → O(sqrt(n))
3. **Total extra space:** O(sqrt(n))

**Why time is still O(n):**

- Step 1 (count): O(n)
- Step 3 (save checkpoints): O(n)
- Step 4 (print all chunks in reverse): each node printed exactly once → O(n)
- Total: **O(n) time**

**The classic time-space tradeoff at its finest.**

---

## LAYER 2: Worked Examples

### Example 1: `head = [1, 2, 3, 4]` → prints `4, 3, 2, 1`

**Trace of recursive approach:**

```
printRev(node1)
  printRev(node2)
    printRev(node3)
      printRev(node4)
        printRev(None) → returns
      print(node4.val) → prints 4
    print(node3.val) → prints 3
  print(node2.val) → prints 2
print(node1.val) → prints 1
```

Output: `4, 3, 2, 1` ✓

**Trace of stack approach:**

```
Walk forward, push:    stack = [node1, node2, node3, node4]
Pop and print:         pop node4 → print 4
                       pop node3 → print 3
                       pop node2 → print 2
                       pop node1 → print 1
```

Output: `4, 3, 2, 1` ✓

### Example 2: `head = [0, -4, -1, 3, -5]` → prints `-5, 3, -1, -4, 0`

Same skeleton — recursion descends 5 deep, then unwinds printing each value in reverse. ✓

### sqrt(n) trace with `head = [1, 2, 3, 4, 5, 6, 7, 8, 9]` (n=9, k=3)

**Step 1:** Count → n = 9
**Step 2:** k = sqrt(9) = 3
**Step 3:** Walk and save checkpoints at positions 0, 3, 6:
- `checkpoints = [node(1), node(4), node(7)]`

**Step 4:** Print chunks in reverse order:
- Chunk starting at `node(7)`, end=None: prints 9, 8, 7
- Chunk starting at `node(4)`, end=node(7): prints 6, 5, 4
- Chunk starting at `node(1)`, end=node(4): prints 3, 2, 1

Output: `9, 8, 7, 6, 5, 4, 3, 2, 1` ✓

Space used: 3 checkpoints + recursion depth ≤ 3 = **O(sqrt(n))** ✓

### Edge cases

- **Single node:** `head = [5]` → recursion goes 1 deep → prints `5`
- **None head:** `if head is not None` guard → no-op
- **All same values:** Just repeats them in reverse
- **Max n=1000:** Within Python's default recursion limit

---

## LAYER 3: Key Insights

| Approach | Time | Space | When to use |
|----------|------|-------|-------------|
| **Recursive (editorial)** | O(n) | O(n) call stack | **Default — clean & correct** |
| Iterative stack | O(n) | O(n) explicit | Avoids recursion limit |
| O(1) space brute | O(n²) | O(1) | If space EXTREMELY tight |
| **sqrt(n) decomposition** ⭐ | **O(n)** | **O(sqrt(n))** | **Follow-up answer** |

**The "recurse then act" pattern (Post-order):**

```python
def process(node):
    if node is None: return
    process(node.next)      # 1. RECURSE FIRST
    do_something(node)      # 2. ACT AFTER
```

Identical to **post-order tree DFS** (LC 94 inorder, LC 104 max depth). The recursive call happens before the work, so work executes in reverse traversal order.

Contrast with "act then recurse" (pre-order):
```python
def process(node):
    if node is None: return
    do_something(node)      # 1. ACT FIRST
    process(node.next)      # 2. RECURSE AFTER
```

Pre-order prints in forward order. **The position of the recursive call determines the print order.** A subtle but powerful insight.

**Why this problem teaches sqrt(n) decomposition:**

`sqrt(n)` is the **geometric mean** of 1 and n. When you face a time-space tradeoff between:
- O(n²) time, O(1) space (brute force)
- O(n) time, O(n) space (full memory)

There's often a sweet spot at **O(n) time, O(sqrt(n)) space** — divide the problem into sqrt(n) chunks of size sqrt(n).

**Other places sqrt(n) decomposition appears:**

- **Mo's algorithm:** offline range queries in O((n+q) × sqrt(n))
- **Sqrt decomposition (block decomposition):** point updates + range queries in O(sqrt(n))
- **Heavy-light decomposition:** tree path queries
- **String matching with sqrt buckets**

**The "checkpoint" pattern:**

> Walking forward through a structure you can't walk back through, save every k-th position as a checkpoint. To process backward, jump to each checkpoint in reverse and process its chunk locally.

This generalizes:
- **Reverse-print this problem:** chunks of size sqrt(n)
- **Reverse linked list with O(1) space (mutable):** use prev pointer trick instead
- **Find k-th from end (LC 19):** two pointers k apart (special case: k=1 checkpoint)

**Why "Immutable" matters:**

If the list were **mutable**, you could just reverse it in place with three pointers (LC 206). That would be O(n) time, O(1) space. The immutability constraint forces us to choose between recursion stack, explicit stack, or sqrt decomposition.

**Connection to LC 3263 (today):**

| LC 3263 | LC 1265 |
|---------|---------|
| Walk forward, collect into array | Walk forward, print backward |
| Trivial — single pointer | Requires reversal trick |
| O(n) time, O(n) output space | O(n) time, O(n)/O(sqrt(n)) extra |

LC 3263 was forward traversal. LC 1265 is the **first "reverse access" linked list problem** — a small step in complexity, but a big step in technique.

**Python recursion limit:**

Python's default recursion limit is around **1000**. The problem constraint says `length ≤ 1000` — **right at the boundary**. If you submit the recursive solution and it fails with `RecursionError`, increase the limit:

```python
import sys
sys.setrecursionlimit(10000)
```

---

## LAYER 4: Interview Variations

• **Mutable list — reverse in place:** Three-pointer reversal (LC 206). O(1) space.
• **Print every k-th node in reverse:** Sqrt(n) checkpoint idea, skip k-1 between prints.
• **Reverse in groups of k (LC 25):** Per-group reversal — same chunk idea.
• **Doubly linked list reverse traversal:** Just walk `prev`. O(1) trivially.
• **Print middle to ends:** Combine slow/fast (LC 876) + chunked reversal.
• **Stream LL reverse:** Sliding window of last k nodes + sqrt blocks.
• **Print in order, skip k:** Forward traversal with counter.
• **Recursive depth-limited:** Use sqrt(n) to avoid recursion limit.
• **LL to array, then reverse:** Two-pass approach.
• **Print TWO lists interleaved in reverse:** Recurse both simultaneously.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Editorial recursion
class Solution:
    def printLinkedListInReverse(self, head: 'ImmutableListNode') -> None:
        if head is not None:
            self.printLinkedListInReverse(head.getNext())
            head.printValue()
```

**Iterative stack:**
```python
def printLinkedListInReverse(self, head):
    stack = []
    curr = head
    while curr is not None:
        stack.append(curr)
        curr = curr.getNext()
    while stack:
        stack.pop().printValue()
```

**O(1) space, O(n²) time:**
```python
def printLinkedListInReverse(self, head):
    n = 0
    curr = head
    while curr is not None:
        n += 1
        curr = curr.getNext()
    for i in range(n - 1, -1, -1):
        curr = head
        for _ in range(i):
            curr = curr.getNext()
        curr.printValue()
```

**sqrt(n) decomposition — the gem:**
```python
import math

class Solution:
    def printLinkedListInReverse(self, head):
        n = 0
        curr = head
        while curr is not None:
            n += 1
            curr = curr.getNext()
        
        k = max(1, int(math.sqrt(n)))
        
        checkpoints = []
        curr = head
        for i in range(n):
            if i % k == 0:
                checkpoints.append(curr)
            curr = curr.getNext()
        
        def print_chunk(node, end):
            if node is end:
                return
            print_chunk(node.getNext(), end)
            node.printValue()
        
        for i in range(len(checkpoints) - 1, -1, -1):
            end = checkpoints[i + 1] if i + 1 < len(checkpoints) else None
            print_chunk(checkpoints[i], end)
```

---

**Time:** O(n) recursion/stack, O(n²) brute, O(n) sqrt | **Space:** O(n), O(1), or O(sqrt(n))

**Pattern flag:** "Process a sequence in reverse without random access" → **`recurse first, act after`** (post-order recursion) OR **explicit stack** (push everything, pop to reverse).

If memory matters more than time, **brute force walking** gives O(1) space at O(n²) time.

For the **best tradeoff**, use **sqrt(n) decomposition** — save sqrt(n) "checkpoints" at evenly spaced positions; process each chunk's content using local recursion of depth sqrt(n). Net: O(n) time, O(sqrt(n)) space.

This is the **time-space tradeoff in miniature.** Same technique appears in Mo's algorithm, sqrt block decomposition, segment trees with lazy propagation. Recognize the **"chunked checkpoint"** technique; you'll use it again.

🎯 **First Medium Linked List on the log.** Demonstrates: recursion as implicit stack, explicit stack as alternative, and the sqrt(n) tradeoff that defines algorithmic maturity.
