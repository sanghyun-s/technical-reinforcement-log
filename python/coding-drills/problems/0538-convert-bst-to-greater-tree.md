# 538. Convert BST to Greater Tree

| | |
|---|---|
| **Difficulty** | Medium |
| **Family** | Trees (BST / reverse inorder) |
| **Date** | 2026-07-26 |
| **Mode** | First-Pass Assisted (new problem, AI-researched) |
| **Link** | https://leetcode.com/problems/convert-bst-to-greater-tree/ |
| **Twin** | Same problem as archived **1038** (re-drill of 1038 set this up) |

---

## 1. Input / Output
```
input  → root = [4,1,6,0,2,5,7,null,null,null,3,null,null,null,8]
output → [30,36,21,36,35,26,15,...]
```
Each node becomes: its own key + the sum of all keys greater than it.

## 2. Data structure used
- The tree + a running `total` carried across a reverse-inorder traversal.

## 3. Why this approach works
**Reverse inorder = descending order in a BST.** Visit **right → node → left**, carrying a
running `total`. By the time any node is reached, every larger value has already been added to
`total`, so `node.val += total` (then `total = node.val`) is exactly "this key plus everything
greater." One pass, each node touched once.

The contrast that makes this click: my archived 1038 solution collected all values, reversed to
descending, and *re-summed everything greater for each node* — correct but **O(n²)**, because it
recomputes overlapping sums. The optimal version **carries the accumulator forward** instead of
recomputing it. Same lesson as merge sort week: O(n²) → O(n) is usually "stop recomputing what
you could carry."

## 4. Time / space complexity
- Time: O(n) — single traversal. (vs O(n²) for the collect-and-re-sum approach.)
- Space: O(h) for the call/explicit stack.

---

## My solution
```python
# Recursive reverse-inorder
class Solution:
    def __init__(self):
        self.total = 0
    def convertBST(self, root):
        if root:
            self.convertBST(root.right)   # larger values first
            self.total += root.val
            root.val = self.total
            self.convertBST(root.left)    # then smaller
        return root
```
Iterative version (robust to deep trees) — **note the bug I hit:**
```python
    def convertBST(self, root):
        total, node, stack = 0, root, []
        while stack or node:
            while node:
                stack.append(node)
                node = node.right    # BUG I HIT: typed `noe = node.right`, so node never
                                     # advanced → infinite loop → ran out of memory
            node = stack.pop()
            total += node.val
            node.val = total
            node = node.left
        return root
```

## What tripped me up
The iterative version failed with a memory error — **not** a LeetCode limit catching a valid
solution, but my own **infinite loop**: I wrote `noe = node.right` instead of `node = ...`, so
the pointer never advanced and the stack grew until memory ran out. Third time this exact class
of bug appeared (merge-sort pointer, quicksort recursion, now this): *the thing that must change
each iteration silently doesn't.* A correct O(n) solution never approaches the memory ceiling.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | **Owed** |
| **Where I got stuck** | Watch the pointer-advance line in the iterative version |

## Interview sentence
> To make each node hold its key plus everything greater, I do a reverse-inorder traversal —
> right, node, left — carrying a running total, so it's one O(n) pass. Collecting values and
> re-summing per node also works but is O(n²) because it recomputes overlapping sums.
