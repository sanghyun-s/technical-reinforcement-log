# 2415. Reverse Odd Levels of Binary Tree

**Difficulty:** Medium
**Pattern:** Tree DFS / Mirror Recursion / Value Swap
**Date Solved:** 2026-06-14
**Status:** ✅

---

## Understanding the Goal

In a **perfect binary tree**, reverse the values at every **odd-indexed level** (1, 3, 5, ...). Even levels (0, 2, 4, ...) stay unchanged.

**The naive approach:** BFS collect each level's nodes, reverse the value list, write back. Works in O(n) but feels procedural.

**The elegant approach (Mirror DFS):** reversing a list = swapping mirror pairs (leftmost with rightmost, etc.). For a perfect binary tree, mirror pairs are reached by **descending two pointers in mirror fashion**:

- Start at `(root.left, root.right)` — mirror pair at level 1
- Recurse on `(left.left, right.right)` — outer mirror pair
- Recurse on `(left.right, right.left)` — inner mirror pair

At each odd level, **swap the two pointers' values**. The recursion pairs symmetric nodes without building a list.

Same template as **LC 101 Symmetric Tree** — "DFS with two pointers walking in mirror."

---

## LAYER 1: Line-by-Line Explanation

### Approach 1: Mirror DFS (Editorial) ⭐

```python
class Solution:
    def reverseOddLevels(self, root) -> TreeNode:
        # Mirror DFS — pass TWO pointers descending in mirror fashion
        def mirror_dfs(left, right, level):
            # Base case: perfect tree, both None at the same time
            if left is None or right is None:
                return
            
            # Swap VALUES (not nodes) at odd levels
            if level % 2 == 1:
                left.val, right.val = right.val, left.val
            
            # Recurse to next level with MIRROR pairings:
            # Outer pair: (left.left, right.right) — extremes meet extremes
            # Inner pair: (left.right, right.left) — meet in the middle
            mirror_dfs(left.left, right.right, level + 1)
            mirror_dfs(left.right, right.left, level + 1)
        
        if root:
            mirror_dfs(root.left, root.right, 1)
        return root
```

**Why mirror recursion pairs the right nodes:**

At level k, the leftmost node and rightmost node are paired. Their children at level k+1:
- Leftmost's LEFT → absolute leftmost of level k+1
- Rightmost's RIGHT → absolute rightmost of level k+1
- Leftmost's RIGHT → second-leftmost
- Rightmost's LEFT → second-rightmost

So **outer pair** descends to (left.left, right.right) — extremes.
The **inner pair** descends to (left.right, right.left) — closer to middle.

By induction, all mirror pairs at every level are visited.

**Why "swap values, not nodes":**

Swapping nodes would require updating parent pointers. **Swapping just `.val`** achieves the same observable result with O(1) work per pair. Tree shape unchanged; only data moves.

**Python tuple-swap idiom — `a, b = b, a`:**

Same idiom as LC 226 (Invert Binary Tree, Day 9) — Python evaluates the right side fully before unpacking to the left. No temp variable needed.

### Approach 2: BFS Level-by-Level

```python
from collections import deque

class Solution:
    def reverseOddLevels(self, root):
        if not root:
            return root
        
        queue = deque([root])
        level = 0
        
        while queue:
            size = len(queue)
            level_nodes = []
            for _ in range(size):
                node = queue.popleft()
                level_nodes.append(node)
                if node.left:
                    queue.append(node.left)
                    queue.append(node.right)
            
            # Reverse VALUES if odd level
            if level % 2 == 1:
                n = len(level_nodes)
                for i in range(n // 2):
                    level_nodes[i].val, level_nodes[n - 1 - i].val = (
                        level_nodes[n - 1 - i].val,
                        level_nodes[i].val,
                    )
            
            level += 1
        
        return root
```

Standard level-aware BFS (the `size = len(queue)` template). For each level, decide whether to reverse based on parity, then swap values via two-pointer from both ends.

---

## LAYER 2: Worked Examples

### Example 1: `root = [2, 3, 5, 8, 13, 21, 34]` → `[2, 5, 3, 8, 13, 21, 34]`

```
Before:               After:
        2                       2
       / \                     / \
      3   5                   5   3      ← level 1 reversed
     /\   /\                 /\   /\
    8 13 21 34              8 13 21 34   ← level 2 unchanged
```

**Mirror DFS trace:**

`mirror_dfs(node(3), node(5), level=1)`:
- level 1 odd → SWAP node(3).val ↔ node(5).val
- Now position root.left has val=5, root.right has val=3
- Recurse outer: `(node(8), node(34), level=2)` — no swap (even); recurse to None children
- Recurse inner: `(node(13), node(21), level=2)` — no swap; recurse to None children

Final level-order: 2, 5, 3, 8, 13, 21, 34 ✓

### Example 3: Deeper tree

Input: `[0,1,2,0,0,0,0,1,1,1,1,2,2,2,2]`

```
                     0                          (level 0, even)
                  /     \
                 1       2                      (level 1, ODD → reverse)
               /  \    /   \
              0    0  0     0                   (level 2, even)
             /\   /\  /\   /\
            1 1  1 1 2 2  2 2                   (level 3, ODD → reverse)
```

Output: `[0,2,1,0,0,0,0,2,2,2,2,1,1,1,1]`

Level 3 has 8 nodes; 4 mirror pairs:
- (L0, L7): outermost → swapped via outer-chain recursion
- (L1, L6): swapped
- (L2, L5): swapped
- (L3, L4): innermost → swapped via all-inner recursion

All 4 pairs covered by mirror DFS. ✓

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **Mirror DFS** ⭐ | **O(n)** | **O(log n)** recursion | **Elegant; preferred** |
| BFS level-by-level | O(n) | O(width) = O(n/2) | Easier to explain |

For a perfect binary tree, depth = log₂(n), so DFS recursion is O(log n) space — much better than BFS's O(n/2).

**The Mirror DFS template (memorize):**

```python
def mirror_dfs(left, right):
    if not left or not right:
        return
    # Operate on pair (left, right)
    
    mirror_dfs(left.left, right.right)    # outer
    mirror_dfs(left.right, right.left)    # inner
```

Same structure as:
- **LC 101 Symmetric Tree** — check pair equality
- **LC 951 Flip Equivalent Trees** — try both orientations
- **LC 2415 (this)** — swap values at odd levels

**The "swap values, not nodes" insight:**

In tree problems, ask: **can I leave the structure alone and just swap data?** Usually yes — O(1) per swap vs O(restructure cost).

Applied here: tree's SHAPE is identical before/after; only `.val` fields move.

Compare to **LC 226 Invert Binary Tree (Day 9)**: swaps `node.left` and `node.right` (modifying STRUCTURE). Could LC 226 be solved by swapping values? No — subtrees have different sizes; structure swap is necessary there.

So: swap **values** when levels have same shape; swap **pointers** when restructuring is required.

**"Perfect binary tree" — why this constraint matters:**

A perfect binary tree guarantees that at any level, the **left subtree of the leftmost mirrors the right subtree of the rightmost**. Without this property, mirror DFS would visit unbalanced pairs (one side None while other has children).

For arbitrary (non-perfect) binary trees, use BFS instead — mirror trick wouldn't generalize cleanly.

**Connection to past tree problems:**

| Problem | Pattern | Today's connection |
|---------|---------|---------------------|
| LC 100 (Day 9) Same Tree | Parallel DFS | **Two pointers descending together** |
| LC 226 (Day 9) Invert Tree | DFS + tuple swap | **Same tuple swap idiom** |
| LC 3831 (today) Level Median | BFS level-aware | Different traversal mode |

LC 2415 is essentially **LC 100's two-pointer DFS** + **LC 226's tuple swap**. You already have both ingredients — today is composition.

**The "two-pointer DFS" generalization:**

Many tree problems involve TWO related nodes:
- LC 101 Symmetric Tree (mirror)
- LC 100 Same Tree (same-side)
- LC 951 Flip Equivalent
- LC 236 LCA queries
- LC 2415 Reverse Odd Levels

Skeleton:
```python
def dfs(a, b):
    if not a or not b: return base
    # operate on pair
    dfs(a.left, b.left)     # same-side
    dfs(a.right, b.right)
    # OR mirror-side:
    dfs(a.left, b.right)
    dfs(a.right, b.left)
```

**Same-side vs mirror-side** depends on semantics:
- Same-side → comparing identical structures (LC 100)
- Mirror-side → comparing/swapping mirrors (LC 101, 2415)

---

## LAYER 4: Interview Variations

• **Reverse EVEN levels instead:** Change `level % 2 == 1` to `level % 2 == 0`.
• **Reverse SPECIFIC level only:** Pass target; swap only when matched.
• **Reverse only LEAVES:** BFS to find leaves; reverse that list.
• **Rotate levels by k:** Cyclic rotation; harder.
• **Non-perfect tree:** Mirror DFS breaks; use BFS.
• **N-ary tree:** Replace `node.left, node.right` with iteration over children.
• **Swap NODES instead of values:** Restructure parent pointers — much harder.
• **Count swaps performed:** Add counter.
• **Detect if reversal changed anything (palindromic):** Compare before/after.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Mirror DFS (O(log n) space)
class Solution:
    def reverseOddLevels(self, root) -> TreeNode:
        def mirror_dfs(left, right, level):
            if not left or not right:
                return
            if level % 2 == 1:
                left.val, right.val = right.val, left.val
            mirror_dfs(left.left, right.right, level + 1)
            mirror_dfs(left.right, right.left, level + 1)
        if root:
            mirror_dfs(root.left, root.right, 1)
        return root
```

**BFS level-by-level:**
```python
from collections import deque

class Solution:
    def reverseOddLevels(self, root):
        if not root: return root
        queue = deque([root])
        level = 0
        while queue:
            size = len(queue)
            nodes = []
            for _ in range(size):
                node = queue.popleft()
                nodes.append(node)
                if node.left:
                    queue.append(node.left)
                    queue.append(node.right)
            if level % 2 == 1:
                n = len(nodes)
                for i in range(n // 2):
                    nodes[i].val, nodes[n - 1 - i].val = nodes[n - 1 - i].val, nodes[i].val
            level += 1
        return root
```

**Reverse a SPECIFIC level (variation):**
```python
class Solution:
    def reverseLevel(self, root, target):
        def mirror_dfs(left, right, level):
            if not left or not right: return
            if level == target:
                left.val, right.val = right.val, left.val
            elif level < target:
                mirror_dfs(left.left, right.right, level + 1)
                mirror_dfs(left.right, right.left, level + 1)
        if root:
            mirror_dfs(root.left, root.right, 1)
        return root
```

---

**Time:** O(n) | **Space:** O(log n) mirror DFS, O(width) BFS

**Pattern flag:** "Reverse / pair-swap content at certain levels of a perfect binary tree" → **Mirror DFS**: pass two pointers descending mirror-symmetrically. Visit `(left.left, right.right)` for outer pair and `(left.right, right.left)` for inner pair. Swap when conditions match.

The mirror DFS is the **same template as LC 101 Symmetric Tree**. Combine with value-swap (LC 226's tuple-swap idiom) and you get LC 2415 for free.

**"Swap values, not nodes"** is a powerful insight when STRUCTURE is preserved. Shape unchanged, only data moves — O(1) per swap, no parent-pointer updates.

The **"perfect binary tree"** constraint is what enables mirror DFS to be clean. Without it, mirror pairs have mismatched None-counts.

🎯 **First mirror-recursion DFS on the log.** Tree toolkit now includes: DFS (5 flavors from Day 9), level-aware BFS, mirror DFS.
