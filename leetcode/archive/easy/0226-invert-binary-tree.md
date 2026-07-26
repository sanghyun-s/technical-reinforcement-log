# 226. Invert Binary Tree

**Difficulty:** Easy
**Pattern:** Tree / DFS / Mutation (Swap Children)
**Date Solved:** 2026-06-05
**Status:** ✅

---

## Understanding the Goal

Take a binary tree and **mirror it** — at every node, swap left and right children. Result is the original reflected across vertical axis.

**Key insight:** Simplest **tree mutation** problem. At every node, swap pointers. Recursion rolls the mutation through the entire tree.

Same DFS skeleton as LC 94/100/104, but: **instead of producing a value, we modify the tree and return the modified root.**

**Historical trivia:** This problem went viral in 2015 — a tweet about Google rejecting a candidate for not solving it on a whiteboard.

---

## LAYER 1: Line-by-Line Explanation

### Editorial — recursive with explicit assignment

```python
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        # Base case: nothing to invert in empty subtree
        if not root:
            return None
        
        # Recursively invert each subtree FIRST, capturing inverted roots
        right = self.invertTree(root.right)   # Invert right subtree
        left = self.invertTree(root.left)     # Invert left subtree
        
        # Swap: attach inverted-right where left was, vice versa
        root.left = right                     # Now-inverted right → becomes left
        root.right = left                     # Now-inverted left → becomes right
        
        return root                           # Return this (now-swapped) node
```

**Reading:** Function recursively asks each subtree to invert itself, then swaps attachment points. The recursive calls MUTATE the subtrees AND return them.

**Why store results in `right`/`left` BEFORE assigning:**

This avoids a subtle bug. If you wrote:
```python
root.left = self.invertTree(root.right)
root.right = self.invertTree(root.left)   # ⚠️ root.left was just overwritten!
```
The second call would invert the WRONG subtree. **Compute both, then commit** sidesteps this — same pattern as the bit-packing trick in LC 1920.

### Cleaner Pythonic tuple-swap

```python
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None
        # Python tuple-swap: evaluate RHS fully into tuple, then assign both at once
        # Same trick as a, b = b, a — safe simultaneous swap
        root.left, root.right = self.invertTree(root.right), self.invertTree(root.left)
        return root
```

**How tuple-swap works:** Python evaluates the entire RHS into a tuple first, then unpacks. No intermediate state where one is overwritten before the other is computed.

### Iterative with stack

```python
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None
        
        stack = [root]                          # Nodes pending the swap
        
        while stack:
            node = stack.pop()
            
            # Swap this node's children
            node.left, node.right = node.right, node.left
            
            # Schedule children for processing
            if node.left:                       # Note: node.left is now OLD node.right
                stack.append(node.left)
            if node.right:
                stack.append(node.right)
        
        return root
```

**Why iterative works the same:** Each node only needs ONE operation — swap its two children. Order doesn't matter (top-down vs bottom-up). So any traversal works.

This is DIFFERENT from problems where order matters (like inorder traversal). Here, every node's work is independent.

### Iterative with BFS

```python
from collections import deque

class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None
        
        queue = deque([root])
        while queue:
            node = queue.popleft()
            node.left, node.right = node.right, node.left
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        return root
```

---

## LAYER 2: Worked Examples

### Example 1: `root = [4, 2, 7, 1, 3, 6, 9]` → `[4, 7, 2, 9, 6, 3, 1]`

**Before:**
```
        4
       / \
      2   7
     / \ / \
    1  3 6  9
```

**After:**
```
        4
       / \
      7   2
     / \ / \
    9  6 3  1
```

**Recursive trace** of `invertTree(4)`:

```
invertTree(4):
  right = invertTree(7):
    right = invertTree(9): return 9 (no children)
    left = invertTree(6): return 6
    7.left = 9, 7.right = 6
    return 7
  left = invertTree(2):
    right = invertTree(3): return 3
    left = invertTree(1): return 1
    2.left = 3, 2.right = 1
    return 2
  4.left = 7, 4.right = 2
  return 4
```

Level-order: `[4, 7, 2, 9, 6, 3, 1]` ✓

### Example 2: `root = [2, 1, 3]` → `[2, 3, 1]`

```
   2          2
  / \   →    / \
 1   3      3   1
```

### Example 3: `root = []` → `[]`

Empty tree → returns None.

### Iterative trace on Example 2

Stack: `[2]`

| step | popped | action | stack after |
|------|--------|--------|-------------|
| 1 | 2 | swap (1,3) → 2.left=3, 2.right=1; push 3 and 1 | [3, 1] |
| 2 | 1 | swap (None, None) — no-op; no children | [3] |
| 3 | 3 | no-op | [] |

### Edge cases
- Empty: returns None
- Single node: swaps two None children (no-op); returns the node
- Palindromic structure: invert produces identical-looking tree
- Deeply skewed n=10⁴: recursion limit risk → use iterative

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Recursive DFS | O(n) | O(h) | **Cleanest** |
| Iterative DFS (stack) | O(n) | O(h) | Avoids recursion limit |
| Iterative BFS (queue) | O(n) | O(w) | Same algorithm, level-by-level |

**Two key concepts:**

1. **Tree mutation:** Trees aren't immutable. You can rewrite pointers. LC 94/100/104 only READ from the tree.

2. **Safe simultaneous assignment:** Must capture old values before overwriting. Either explicit two-variable or Python's tuple-swap.

**Why this is "Easy" but conceptually important:**

Tests:
- Do you understand tree node fields are mutable?
- Do you understand "compute both, then assign"?
- Do you understand traversal ORDER doesn't matter when each node's work is independent?

These generalize to tree rebalancing, AVL/red-black rotations, B-tree splits, persistent data structures.

**Why traversal order doesn't matter here:**

Each node depends only on its own children. Top-down, bottom-up, DFS, BFS — all produce same final tree.

NOT true for many tree problems:
- **Inorder:** order matters (left → node → right)
- **Path sum:** must accumulate from root down
- **Diameter:** must aggregate from leaves up

When work is "embarrassingly parallel per-node," traversal order is your free choice.

**The "swap" pattern:**

- Invert (this): swap left ↔ right
- Mirror check (LC 101): check if left subtree's invert == right
- Symmetric BST construction: mirror a sorted structure

**Connection to today's tree problems:**

| Problem | What DFS returned | What it did |
|---------|---------------------|-------------|
| LC 94 Inorder | list of values | read-only |
| LC 100 Same Tree | bool | read-only |
| LC 104 Max Depth | integer | read-only |
| LC 226 Invert (this) | the root | **mutates: swaps children** |

Four flavors of DFS in one day: list-building, predicate, aggregation, mutation.

---

## LAYER 4: Interview Variations

• **Symmetric Tree (LC 101):** Check if tree is its own mirror. Parallel DFS comparing `(left.left, right.right)` and `(left.right, right.left)`.
• **Invert WITHOUT modifying input:** Build new tree as you recurse.
• **Invert only subtree at target node:** Find target first, invert from there.
• **Invert at every K-th level:** Pass depth; swap only when `depth % K == 0`.
• **Rotate tree by 90 degrees:** Actual structural rotation; more complex.
• **N-ary tree inversion:** Reverse children list: `node.children = node.children[::-1]`.
• **Reverse linked list:** Similar concept; three-pointer technique.
• **Verify two trees are mirrors:** Apply invert, check isSameTree. Or use LC 101 logic directly.
• **Find which nodes change:** Compare original to inverted.
• **Persistent invert (functional):** Build new tree without mutating original.
• **Random invert:** Probabilistic at each node — tree randomization.
• **With parent pointers:** Just swap left/right; parent pointers unchanged.
• **Pre-compute inversion map:** "Where did node X end up?" queries.
• **Streaming tree:** Maintain invert as nodes inserted.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Pythonic tuple-swap
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return None
        root.left, root.right = self.invertTree(root.right), self.invertTree(root.left)
        return root
```

**Editorial — explicit two-variable:**
```python
def invertTree(root):
    if not root:
        return None
    right = invertTree(root.right)
    left = invertTree(root.left)
    root.left = right
    root.right = left
    return root
```

**Iterative with stack:**
```python
def invertTree(root):
    if not root:
        return None
    stack = [root]
    while stack:
        node = stack.pop()
        node.left, node.right = node.right, node.left
        if node.left: stack.append(node.left)
        if node.right: stack.append(node.right)
    return root
```

**Iterative with BFS:**
```python
from collections import deque
def invertTree(root):
    if not root:
        return None
    queue = deque([root])
    while queue:
        node = queue.popleft()
        node.left, node.right = node.right, node.left
        if node.left: queue.append(node.left)
        if node.right: queue.append(node.right)
    return root
```

**Build new tree (non-destructive):**
```python
def invertTree(root):
    if not root:
        return None
    new_root = TreeNode(root.val)
    new_root.left = invertTree(root.right)
    new_root.right = invertTree(root.left)
    return new_root
```

---

**Time:** O(n) | **Space:** O(h) DFS / O(w) BFS

**Pattern flag:** "Modify tree structure (swap pointers, redirect)" → **DFS with mutation**. When each node's work depends only on its own children, traversal order is free. Critical Python idiom: **`a, b = b, a` for safe swap**; same trick works for tree pointers. When recursion would overwrite before reading, compute RHS fully into a tuple, then assign in one step.
