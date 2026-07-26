# 100. Same Tree

**Difficulty:** Easy
**Pattern:** Tree / DFS / Parallel Traversal / Structural Comparison
**Date Solved:** 2026-06-05
**Status:** ✅

---

## Understanding the Goal

Compare two binary trees `p` and `q`. "Same" iff:
1. **Structurally identical** — same shape (matching positions of nodes and nulls)
2. **Same values** at every corresponding position

**Key insight:** **Parallel tree recursion** — walk both trees in lockstep. Two trees are "same" iff:
- Roots match (both None, OR both non-None with same value)
- Left subtrees same (recurse)
- Right subtrees same (recurse)

Same DFS skeleton as LC 94, but two cursors instead of one.

---

## LAYER 1: Line-by-Line Explanation

### Editorial — recursive with explicit base cases

```python
class Solution:
    def isSameTree(self, p: TreeNode, q: TreeNode) -> bool:
        # Base case 1: both None → same (matching nulls)
        if not p and not q:
            return True
        
        # Base case 2: exactly one None → different shape
        # (Reached only if both-None check already failed)
        if not q or not p:
            return False
        
        # Both exist. If values differ, trees differ here
        if p.val != q.val:
            return False
        
        # Values match. Recursively check BOTH subtrees
        # `and` short-circuits — bails on first False
        return self.isSameTree(p.right, q.right) and self.isSameTree(p.left, q.left)
```

**The decision tree:**

| `p` | `q` | Action |
|-----|-----|--------|
| None | None | Return `True` (empty matches empty) |
| None | not None | Return `False` (shape mismatch) |
| not None | None | Return `False` (shape mismatch) |
| not None | not None, vals differ | Return `False` |
| not None | not None, vals same | Recurse on children |

**Why second check `if not q or not p`:**

Reaching this line means at least one is NOT None. So if OR is True, exactly one is None.

### Cleaner one-liner

```python
class Solution:
    def isSameTree(self, p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
        if not p and not q:
            return True
        if not p or not q:
            return False
        # Chain value check + both recursions with `and`
        return (p.val == q.val
                and self.isSameTree(p.left, q.left)
                and self.isSameTree(p.right, q.right))
```

### Tightest using `is` trick

```python
class Solution:
    def isSameTree(self, p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
        if not p or not q:
            return p is q        # Both None → True; one None → False
        return (p.val == q.val
                and self.isSameTree(p.left, q.left)
                and self.isSameTree(p.right, q.right))
```

**The `p is q` trick:** If at least one is None, `p is q` is True only when BOTH are None (None is the singleton). Handles both base cases in one line.

⚠️ Use `is`, NOT `==`. `is` checks object identity; `==` might call `__eq__` for custom node types.

### Iterative with stack

```python
class Solution:
    def isSameTree(self, p, q) -> bool:
        stack = [(p, q)]                            # Pairs of nodes to compare
        
        while stack:
            node_p, node_q = stack.pop()
            
            if not node_p and not node_q:           # Both None — continue
                continue
            if not node_p or not node_q:            # One None — mismatch
                return False
            if node_p.val != node_q.val:            # Values differ
                return False
            
            # Schedule children comparisons
            stack.append((node_p.left, node_q.left))
            stack.append((node_p.right, node_q.right))
        
        return True                                 # Drained without mismatch
```

---

## LAYER 2: Worked Examples

### Example 1: `p = [1,2,3]`, `q = [1,2,3]` → True

Both trees identical:
```
    1               1
   / \             / \
  2   3           2   3
```

**Recursive trace:**

```
isSameTree(1, 1):
  Both exist, 1 == 1
  recurse right: isSameTree(3, 3):
    Both exist, 3 == 3
    recurse(None, None) → True
    recurse(None, None) → True
    return True
  recurse left: isSameTree(2, 2):
    similar → True
  return True
```

Final: `True` ✓

### Example 2: `p = [1, 2]`, `q = [1, null, 2]` → False

```
    1                   1
   /                     \
  2                       2
```

```
isSameTree(1, 1):
  Values match.
  recurse right: isSameTree(None, 2):
    Not both None. p None, q not → return False
  Short-circuit: `False and ...` → return False
```

### Example 3: `p = [1, 2, 1]`, `q = [1, 1, 2]` → False

Same shape, different values.

```
isSameTree(1, 1):
  Values match.
  recurse right: isSameTree(1, 2):
    Values differ → False
  Short-circuit → False
```

### Iterative trace on Example 2

Stack: `[(p_root, q_root)]`

| step | pop | check | action |
|------|-----|-------|--------|
| 1 | (1, 1) | both exist, match | push (None, 2) and (2, None) |
| 2 | (2, None) | exactly one None | **return False** |

### Edge cases
- Both empty: first check returns True instantly
- One empty: second check returns False
- Single node each, same val: matches → True
- Single node each, different val: returns False

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Recursive DFS | O(min(n, m)) | O(min(h_p, h_q)) | **Cleanest** |
| Iterative with stack | O(min(n, m)) | O(min(h_p, h_q)) | Avoids recursion limit |
| Serialize + compare | O(n + m) | O(n + m) | Wasteful |

**Why O(min) not O(max):**

Short-circuit on first mismatch. Worst case (identical) visits every node = O(n) = O(m).

**The parallel-recursion pattern (one of the most reusable):**

```python
def compare(a, b):
    if not a and not b:           # Both base case
        return base_result
    if not a or not b:             # One done, one not
        return mismatch_result
    return (condition(a, b)
            and compare(a.left, b.left)
            and compare(a.right, b.right))
```

Solves:
- LC 100 Same Tree (values must match)
- LC 101 Symmetric Tree (recurse `(a.left, b.right)` and `(a.right, b.left)`)
- LC 572 Subtree of Another Tree
- LC 951 Flip Equivalent Trees

**Base case order matters:**

```python
# CORRECT
if not p and not q: return True       # Both None: success
if not p or not q: return False        # One None: failure

# WRONG
if not p or not q: return False        # False even when BOTH None
```

Both-None check MUST come first.

**Tree equality vs identity:**

VALUE equality, not memory identity. The `p is q` shortcut works at base case ONLY because Python's `None` is a singleton.

**Connection to LC 94 (yesterday):**

Both are DFS. LC 94 walked one tree producing a list; this walks two producing a bool. Skeleton identical — only the "process" step differs.

---

## LAYER 4: Interview Variations

• **Symmetric Tree (LC 101):** Check if single tree is mirror of itself. Recurse with `(a.left, b.right)` and `(a.right, b.left)`.
• **Subtree of Another Tree (LC 572):** Check if `t` appears in `s`. Use `isSameTree` as helper; recurse on `s.left` and `s.right`.
• **Flip Equivalent (LC 951):** Match either same-side OR flipped.
• **Count matching nodes:** Accumulate count instead of bool.
• **Largest identical subtree:** Hash subtree structures (Merkle-style).
• **N-ary trees:** Compare children lists in order with `zip`.
• **Compare values only (ignore structure):** Sort/multiset both, compare.
• **Wildcard nulls:** Add wildcard case in base.
• **Find first differing node:** Return node value on mismatch instead of False.
• **Float tolerance:** Replace `!=` with `abs(diff) > epsilon`.
• **Generator-based comparison:** Yield triples, zip and compare.
• **Hash-based filter:** Compare Merkle hashes first.
• **Concurrent traversal:** Parallel walkers for huge trees.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Recursive (editorial style)
class Solution:
    def isSameTree(self, p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
        if not p and not q:
            return True
        if not p or not q:
            return False
        if p.val != q.val:
            return False
        return (self.isSameTree(p.left, q.left)
                and self.isSameTree(p.right, q.right))
```

**Compact recursive:**
```python
def isSameTree(p, q):
    if not p and not q:
        return True
    if not p or not q:
        return False
    return (p.val == q.val
            and isSameTree(p.left, q.left)
            and isSameTree(p.right, q.right))
```

**Ultra-compact with `is` trick:**
```python
def isSameTree(p, q):
    if not p or not q:
        return p is q
    return (p.val == q.val
            and isSameTree(p.left, q.left)
            and isSameTree(p.right, q.right))
```

**Iterative with stack:**
```python
def isSameTree(p, q):
    stack = [(p, q)]
    while stack:
        a, b = stack.pop()
        if not a and not b:
            continue
        if not a or not b:
            return False
        if a.val != b.val:
            return False
        stack.append((a.left, b.left))
        stack.append((a.right, b.right))
    return True
```

**BFS with queue:**
```python
from collections import deque
def isSameTree(p, q):
    queue = deque([(p, q)])
    while queue:
        a, b = queue.popleft()
        if not a and not b:
            continue
        if not a or not b:
            return False
        if a.val != b.val:
            return False
        queue.append((a.left, b.left))
        queue.append((a.right, b.right))
    return True
```

---

**Time:** O(min(n, m)) | **Space:** O(min(h_p, h_q))

**Pattern flag:** "Compare two trees structurally" → **parallel DFS**. Walk both in lockstep with paired recursion. Three base cases: both None (match), exactly one None (mismatch), both exist (compare and recurse). Extends to symmetric checks, subtree matching, mirror equivalence — change only recursion arguments. Memorize the skeleton.
