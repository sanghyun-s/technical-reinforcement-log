# 938. Range Sum of BST

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | Trees (BST / DFS) |
| **Date** | 2026-07-26 |
| **Mode** | Reference Recall (re-drill of archived 0938; archive open) |
| **Link** | https://leetcode.com/problems/range-sum-of-bst/ |

---

## 1. Input / Output
```
input  → root = [10,5,15,3,7,null,18], low = 7, high = 15
output → 32   (7 + 10 + 15)
```
Constraints that matter:
- Up to 2×10⁴ nodes → an O(n) traversal is fine, but pruning makes it faster in practice.
- It's a **BST**, not a plain tree — that ordering is the whole optimization.

## 2. Data structure used
- The tree itself + an explicit `stack` (iterative) or the call stack (recursive).

## 3. Why this approach works
**BST pruning.** Everything in a node's left subtree is `< node.val`, everything right is
`> node.val`. So if `node.val <= low`, the entire left subtree is below range — skip it; if
`node.val >= high`, the whole right subtree is above range — skip it. I only descend where
in-range values could exist, instead of visiting all N nodes like a plain-tree sum would force.
The strict inequalities (`if low < node.val`, `if node.val < high`) are deliberate: when
`node.val == low`, the left subtree is entirely `< low`, so descending gains nothing.

## 4. Time / space complexity
- Time: O(n) worst case, closer to O(log n + k) on a balanced tree thanks to pruning.
- Space: O(h), tree height, for the stack / call stack.

---

## My solution
```python
# Iterative — avoids recursion-depth limits on a deep tree
class Solution:
    def rangeSumBST(self, root, low, high):
        ans, stack = 0, [root]
        while stack:
            node = stack.pop()
            if node:
                if low <= node.val <= high: ans += node.val
                if low < node.val:  stack.append(node.left)
                if node.val < high: stack.append(node.right)
        return ans
```
Cleanest variant (recursive, returns its own subtotal — no shared mutable state):
```python
    def dfs(node):
        if not node: return 0
        total = node.val if low <= node.val <= high else 0
        if low < node.val:  total += dfs(node.left)
        if node.val < high: total += dfs(node.right)
        return total
```

## What tripped me up
Reference was open, so this was reconstruction, not a cold rebuild — recall not yet proven.
The subtle point is `<` vs `<=` on the descent guards; both boundary cases are correct here.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | **Owed** — reference was open this time |
| **Where I got stuck** | — |

## Interview sentence
> I used the BST property to skip whole subtrees that can't hold in-range values, rather than
> summing the entire tree — so it prunes to roughly O(log n + k) on a balanced tree. I default
> to the return-value recursion because it composes without shared mutable state.
