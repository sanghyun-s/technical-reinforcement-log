# 104. Maximum Depth of Binary Tree

**Difficulty:** Easy
**Pattern:** Tree / DFS / Recursive Aggregation
**Date Solved:** 2026-06-05
**Status:** ✅

---

## Understanding the Goal

Return the **number of nodes** along the longest root-to-leaf path. Depth = "height counted in nodes," not edges.

**Reference points:**
- Empty tree: depth = 0
- Single root: depth = 1

**Key insight — clean recurrence:**

```
depth(node) = 0                                    if node is None
depth(node) = 1 + max(depth(left), depth(right))   otherwise
```

Depth of the whole tree = "1 (for this node) + the depth of the deeper subtree." This single-line recurrence IS the algorithm.

This is your introduction to **divide-and-conquer on trees**.

---

## LAYER 1: Line-by-Line Explanation

### Recursive one-liner (preferred)

```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        # Base case: empty tree has depth 0
        if not root:
            return 0
        
        # Recursive: 1 for current node + depth of DEEPER subtree
        return 1 + max(self.maxDepth(root.left), self.maxDepth(root.right))
```

**Reading the recurrence:**
- `if not root: return 0` — empty subtree depth = 0
- `self.maxDepth(root.left)` — deepest path in left subtree
- `self.maxDepth(root.right)` — same for right
- `max(...)` — pick LONGER path
- `1 +` — add for current node

Recursion bottoms out at None, then each parent "rolls up" by adding 1.

### Explicit-helper version

```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        def depth(node):
            if not node:                        # Empty subtree
                return 0
            left_depth = depth(node.left)       # Recurse left
            right_depth = depth(node.right)     # Recurse right
            return 1 + max(left_depth, right_depth)  # Current + deeper
        
        return depth(root)
```

### Iterative DFS with stack

```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        
        # Stack: (node, depth_at_this_node) pairs
        stack = [(root, 1)]                     # Root at depth 1
        max_depth = 0
        
        while stack:
            node, depth = stack.pop()
            max_depth = max(max_depth, depth)   # Track deepest seen
            
            # Push children with depth+1
            if node.left:
                stack.append((node.left, depth + 1))
            if node.right:
                stack.append((node.right, depth + 1))
        
        return max_depth
```

**Why pair node with depth:** When you pop later, you've lost context for how deep it lives. Bundle it.

### BFS level-order

```python
from collections import deque

class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        
        queue = deque([root])
        depth = 0
        
        while queue:
            depth += 1                          # Starting a new level
            # Process EXACTLY one level (len captured at start)
            for _ in range(len(queue)):
                node = queue.popleft()
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
        
        return depth
```

**`for _ in range(len(queue))` matters:** `len(queue)` captured at loop start = count of nodes at current level. Process exactly that many. Canonical "level-by-level BFS" pattern.

---

## LAYER 2: Worked Examples

### Example 1: `root = [3, 9, 20, null, null, 15, 7]` → 3

```
        3
       / \
      9   20
         /  \
        15   7
```

**Recursive trace:**

```
maxDepth(3):
  maxDepth(9):
    maxDepth(None) → 0
    maxDepth(None) → 0
    return 1 + max(0, 0) = 1
  maxDepth(20):
    maxDepth(15): 1 + max(0, 0) = 1
    maxDepth(7): 1
    return 1 + max(1, 1) = 2
  return 1 + max(1, 2) = 3
```

Final: **3** ✓

### Example 2: `root = [1, null, 2]` → 2

```
    1
     \
      2
```

```
maxDepth(1):
  maxDepth(None) → 0
  maxDepth(2): 1 + max(0, 0) = 1
  return 1 + max(0, 1) = 2
```

### Iterative DFS trace on Example 1

Stack: `[(3, 1)]`, max_depth = 0.

| step | popped | max_depth | stack after pushes |
|------|--------|-----------|---------------------|
| 1 | (3, 1) | 1 | [(9, 2), (20, 2)] |
| 2 | (20, 2) | 2 | [(9, 2), (15, 3), (7, 3)] |
| 3 | (7, 3) | 3 | [(9, 2), (15, 3)] |
| 4 | (15, 3) | 3 | [(9, 2)] |
| 5 | (9, 2) | 3 | [] |

Result: **3** ✓

### BFS trace on Example 1

Queue: `deque([3])`, depth = 0.

| outer iter | depth | level nodes | queue after |
|------------|-------|-------------|-------------|
| 1 | 1 | [3] | [9, 20] |
| 2 | 2 | [9, 20] | [15, 7] |
| 3 | 3 | [15, 7] | [] |

Three levels → depth 3. ✓

### Edge cases

- Empty tree: returns 0
- Single node: returns 1
- Left-skewed chain: depth = chain length
- Perfectly balanced n nodes: depth ≈ log₂(n+1)
- Worst case n=10⁴ skewed: recursion limit risk → iterative safer

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Recursive DFS | O(n) | O(h) call stack | **Cleanest, preferred** |
| Iterative DFS | O(n) | O(h) stack | Avoids recursion limit |
| BFS level-order | O(n) | O(w) — max width | Useful when depth is the goal |

**Why O(h) for DFS, O(w) for BFS:**

DFS holds only current path. Balanced tree: log(n). Skewed: n.
BFS holds whole level. Balanced: ~n/2 at widest. Skewed: 1.

Pick by tree shape:
- Wide-shallow: DFS wins memory
- Tall-narrow: BFS wins memory
- Unknown: recursive DFS (cleanest)

**The "divide and conquer on trees" pattern (foundational):**

```python
def solve(node):
    if not node:                # Base case
        return base_value
    left_answer = solve(node.left)
    right_answer = solve(node.right)
    return combine(node.val, left_answer, right_answer)
```

| Problem | base_value | combine |
|---------|-----------|---------|
| **Max depth** | 0 | `1 + max(L, R)` |
| Min depth | careful! | `1 + min(L, R)` with leaf check |
| Count nodes | 0 | `1 + L + R` |
| Sum values | 0 | `node.val + L + R` |
| Max path sum | 0 | `node.val + max(0, L, R, L+R)` |
| Diameter | 0 | depth-tracking + max-update |
| Is balanced | True | `|L - R| ≤ 1 and both balanced` |

Same skeleton, dozens of problems.

**Why `1 +` matters (vs counting edges):**

Problem says **nodes**, not edges. `[1, null, 2]` is depth 2 (two nodes), not 1 (one edge).

**Min depth subtlety:**

`1 + min(L, R)` FAILS for nodes with one None child — wrongly returns 1. Need to handle "leaf vs internal-with-one-child."

---

## LAYER 4: Interview Variations

• **Min depth (LC 111):** Careful! One-None-child case needs handling.
• **Diameter (LC 543):** Longest path between any two nodes. Track depth + global max with `left_depth + right_depth`.
• **Is balanced (LC 110):** `|left - right| ≤ 1`. -1 sentinel trick for O(n).
• **Number of nodes at depth K:** BFS counting at level K.
• **Sum of values at depth K:** BFS level-by-level.
• **All values at max depth:** Find depth first, traverse again.
• **Path sum (LC 112):** Same recursion, subtract `node.val`.
• **Sum of root-to-leaf numbers (LC 129):** Carry running prefix.
• **N-ary max depth (LC 559):** `max(depth(c) for c in node.children)`.
• **Right-side view (LC 199):** BFS, keep last node per level.
• **Vertical column sum:** Track horizontal offset.
• **Iterative without recursion limit:** Use `(node, depth)` stack.
• **Tree height streaming:** Maintain depth per node on insert.
• **Find deepest node value:** Track best reference.
• **Average depth:** Sum depths, divide by count.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Recursive one-liner
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        return 1 + max(self.maxDepth(root.left), self.maxDepth(root.right))
```

**With explicit helper:**
```python
def maxDepth(root):
    def depth(node):
        if not node:
            return 0
        return 1 + max(depth(node.left), depth(node.right))
    return depth(root)
```

**Iterative DFS:**
```python
def maxDepth(root):
    if not root:
        return 0
    stack = [(root, 1)]
    max_depth = 0
    while stack:
        node, d = stack.pop()
        max_depth = max(max_depth, d)
        if node.left:
            stack.append((node.left, d + 1))
        if node.right:
            stack.append((node.right, d + 1))
    return max_depth
```

**BFS level-order:**
```python
from collections import deque
def maxDepth(root):
    if not root:
        return 0
    queue = deque([root])
    depth = 0
    while queue:
        depth += 1
        for _ in range(len(queue)):       # Process EXACTLY one level
            node = queue.popleft()
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
    return depth
```

**Tail-recursion-ish:**
```python
def maxDepth(root, depth=0):
    if not root:
        return depth
    return max(maxDepth(root.left, depth + 1),
               maxDepth(root.right, depth + 1))
```

---

**Time:** O(n) | **Space:** O(h) DFS / O(w) BFS

**Pattern flag:** "Aggregate something from leaves up to root" → **divide-and-conquer DFS**. Skeleton: handle None base, recurse on children, combine. For depth: `combine = 1 + max(L, R)`. Change combine and you solve dozens of tree problems. Most reused tree pattern in interviews.
