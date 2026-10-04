# 1302. Deepest Leaves Sum

**Difficulty:** Medium
**Pattern:** Tree DFS/BFS / Depth Tracking / Level Aggregation
**Date Solved:** 2026-06-14
**Status:** ✅

---

## Understanding the Goal

Find the sum of all values at the **deepest level** of a binary tree.

**This problem composes patterns you already know:**
- **LC 104 Max Depth (Day 9):** DFS aggregation that returns depth
- **LC 3831 Level Median (today):** Level-aware BFS with `size = len(queue)` snapshot
- **LC 94 Inorder (Day 9):** Iterative DFS with explicit stack

The editorial uses iterative DFS with stack. But there's an even **cleaner BFS solution**:

> Process levels one by one. Each iteration, OVERWRITE a running `level_sum`. When the queue empties, the last value assigned is the deepest level's sum.

**The "natural end" of BFS IS the deepest level.** No explicit depth counter needed.

---

## LAYER 1: Line-by-Line Explanation

### Approach 1: BFS — "Just track the last level sum" ⭐ (most elegant)

```python
from collections import deque

class Solution:
    def deepestLeavesSum(self, root: TreeNode) -> int:
        queue = deque([root])
        level_sum = 0                          # gets overwritten each level
        
        while queue:
            level_sum = 0                       # reset for new level
            
            # Process exactly the current level (snapshot the size)
            for _ in range(len(queue)):
                node = queue.popleft()
                level_sum += node.val
                
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            
            # After inner loop: queue holds ONLY next level's nodes
            # If queue empty → this WAS deepest level → level_sum is answer
        
        return level_sum
```

**The key insight — why this works:**

After the inner `for` loop, we've finished one level. The queue now holds children for the NEXT level. If there were no children, the queue is empty.

So the outer `while queue` exits **exactly when we just finished the deepest level**. At that moment, `level_sum` holds the sum we want.

**Compare to LC 3831 (today):**

LC 3831 needed a SPECIFIC level — we tracked `current_level` and stopped at the target. Today, we want the DEEPEST — let BFS run to completion, last `level_sum` is the answer. **Smaller code, no level counter.**

### Approach 2: Iterative DFS with stack (Editorial)

```python
class Solution:
    def deepestLeavesSum(self, root: TreeNode) -> int:
        deepest_sum = depth = 0
        stack = [(root, 0)]
        
        while stack:
            node, curr_depth = stack.pop()
            
            # Leaf check — process only leaves
            if node.left is None and node.right is None:
                if depth < curr_depth:
                    # Deeper leaf found → reset
                    deepest_sum = node.val
                    depth = curr_depth
                elif depth == curr_depth:
                    # Same depth as current max → accumulate
                    deepest_sum += node.val
                # If curr_depth < depth, ignore (shallower)
            else:
                if node.right: stack.append((node.right, curr_depth + 1))
                if node.left: stack.append((node.left, curr_depth + 1))
        
        return deepest_sum
```

**The three-case leaf logic:**

| Comparison | Action |
|-----------|--------|
| `depth < curr_depth` | Reset sum to this leaf; update max depth |
| `depth == curr_depth` | Add to existing sum |
| `depth > curr_depth` | Ignore (shallower leaf) |

### Approach 3: Recursive DFS

```python
class Solution:
    def deepestLeavesSum(self, root):
        self.max_depth = 0
        self.total = 0
        
        def dfs(node, depth):
            if not node: return
            if not node.left and not node.right:
                if depth > self.max_depth:
                    self.max_depth = depth
                    self.total = node.val
                elif depth == self.max_depth:
                    self.total += node.val
                return
            dfs(node.left, depth + 1)
            dfs(node.right, depth + 1)
        
        dfs(root, 0)
        return self.total
```

### Approach 4: Two-pass — Find depth, then sum at depth

```python
class Solution:
    def deepestLeavesSum(self, root):
        def max_depth(node):
            if not node: return 0
            return 1 + max(max_depth(node.left), max_depth(node.right))
        
        target = max_depth(root) - 1
        
        total = 0
        def sum_at(node, depth):
            nonlocal total
            if not node: return
            if depth == target:
                total += node.val
                return
            sum_at(node.left, depth + 1)
            sum_at(node.right, depth + 1)
        
        sum_at(root, 0)
        return total
```

Two passes — first finds depth (reusing LC 104's max depth template), second sums.

---

## LAYER 2: Worked Examples

### Example 1: `[1,2,3,4,5,null,6,7,null,null,null,null,8]` → 15

```
                1                 (level 0)
              /   \
             2     3              (level 1)
            / \     \
           4   5     6            (level 2)
          /           \
         7             8          (level 3) — DEEPEST
```

Deepest leaves: [7, 8] → sum = **15** ✓

**BFS trace:**

| iter | queue start | processed | level_sum | queue after |
|------|-------------|-----------|-----------|-------------|
| 1 | [1] | 1 | 1 | [2, 3] |
| 2 | [2, 3] | 2, 3 | 5 | [4, 5, 6] |
| 3 | [4, 5, 6] | 4, 5, 6 | 15 | [7, 8] |
| 4 | [7, 8] | 7, 8 | **15** | [] |

Queue empty after iter 4 → exit. Final `level_sum = 15`. ✓

### Single-node tree

Root is its own leaf at depth 0. Sum = root.val. ✓

### Skewed tree: `[1, 2, null, 3, null, 4, null, 5]` → 5

Linear chain. Only leaf is 5 at deepest level. Sum = **5** ✓

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **BFS overwrite** ⭐ | **O(n)** | **O(w)** | **Cleanest** |
| DFS iterative + tracking | O(n) | O(h) | Editorial; more complex |
| DFS recursive | O(n) | O(h) | Same logic, recursive |
| Two-pass DFS | O(n) | O(h) | Conceptually clean; 2 traversals |

**The "process levels, save the last" trick:**

```python
queue = deque([root])
result = None                          # gets overwritten each level
while queue:
    result = process_current_level(queue)
    enqueue_next_level(queue)
# After loop: result holds answer for deepest level
```

Reusable for:
- **LC 1302 (this):** sum of deepest leaves
- **LC 513 Find Bottom Left Tree Value:** value of leftmost deepest — track first node of each level
- **Any "deepest" query** — sum, max, count, average, leftmost, rightmost

**BFS vs DFS for "deepest level":**

For "deepest" queries, BFS wins because **the deepest level announces itself** via "no more children to enqueue." DFS would need explicit max-tracking.

**The "leaf vs internal node" decision in DFS:**

The editorial's iterative DFS distinguishes:
- Leaves → check depth, accumulate
- Internal nodes → push children for traversal

Common pattern when "leaves" specifically matter — same skeleton as LC 257 Binary Tree Paths, LC 112 Path Sum, LC 404 Sum of Left Leaves.

**Why we don't need to TRACK the deepest depth explicitly with BFS:**

BFS's nature: "explore all nodes at distance k before any at distance k+1." When queue is empty after processing, no nodes exist at next depth → we just finished the deepest level.

**Depth is implicit in BFS structure.** No counter needed.

**Subtle correctness:** Shallow leaves contribute to their OWN level_sum, which gets OVERWRITTEN as we descend. Only the FINAL level_sum is returned — correct because we want only the deepest.

**Connection to LC 104:** Two-pass DFS (Approach 4) literally reuses LC 104 to find the depth. **Composition of templates** — Day 13's recurring theme.

---

## LAYER 4: Interview Variations

• **Sum at SPECIFIC depth:** Stop BFS at that depth (like LC 3831).
• **Max value at deepest level:** Replace `+=` with `max()`.
• **Count of deepest leaves:** Increment counter.
• **Average at deepest:** sum / count.
• **Leftmost deepest leaf (LC 513):** Track FIRST node of each level.
• **Rightmost deepest:** Track LAST.
• **All deepest leaves (list):** Collect into list per level; return last.
• **Sum at every level (LC 637 cousin):** Build list per level.
• **N-ary tree variant:** Replace `node.left, node.right` with `for child in node.children`.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — BFS overwrite trick
from collections import deque

class Solution:
    def deepestLeavesSum(self, root: TreeNode) -> int:
        queue = deque([root])
        level_sum = 0
        while queue:
            level_sum = 0
            for _ in range(len(queue)):
                node = queue.popleft()
                level_sum += node.val
                if node.left: queue.append(node.left)
                if node.right: queue.append(node.right)
        return level_sum
```

**BFS with queue replacement:**
```python
def deepestLeavesSum(root):
    queue = [root]
    while queue:
        next_level = []
        for node in queue:
            if node.left: next_level.append(node.left)
            if node.right: next_level.append(node.right)
        if not next_level:
            return sum(n.val for n in queue)
        queue = next_level
    return 0
```

**Editorial — iterative DFS:**
```python
def deepestLeavesSum(root):
    deepest_sum = depth = 0
    stack = [(root, 0)]
    while stack:
        node, curr_depth = stack.pop()
        if node.left is None and node.right is None:
            if depth < curr_depth:
                deepest_sum = node.val
                depth = curr_depth
            elif depth == curr_depth:
                deepest_sum += node.val
        else:
            if node.right: stack.append((node.right, curr_depth + 1))
            if node.left: stack.append((node.left, curr_depth + 1))
    return deepest_sum
```

**Recursive DFS with nonlocal:**
```python
def deepestLeavesSum(root):
    max_depth = 0
    total = 0
    def dfs(node, depth):
        nonlocal max_depth, total
        if not node: return
        if not node.left and not node.right:
            if depth > max_depth:
                max_depth = depth
                total = node.val
            elif depth == max_depth:
                total += node.val
            return
        dfs(node.left, depth + 1)
        dfs(node.right, depth + 1)
    dfs(root, 0)
    return total
```

---

**Time:** O(n) | **Space:** O(w) BFS or O(h) DFS

**Pattern flag:** "Find value/sum/property at DEEPEST level" → **BFS with `level_sum = 0` reset each iteration, return after queue empties**. The deepest level is implicit — last level BFS processes.

For ANY "deepest level" query, BFS lets you OVERWRITE the running value each level. Natural end of BFS = deepest level. **No explicit depth counter needed.**

The **leaf check `if not node.left and not node.right`** is the key DFS distinction between leaves and internal nodes. Use it when problems specifically mention leaves.

This problem **composes Day 9's depth tracking (LC 104) + today's level-aware BFS (LC 3831)**. All in your toolkit — today is composition.
