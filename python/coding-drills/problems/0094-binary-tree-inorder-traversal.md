# 94. Binary Tree Inorder Traversal

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | Trees (DFS / explicit stack) |
| **Date** | 2026-08-11 |
| **Mode** | Reference Recall |
| **Link** | https://leetcode.com/problems/binary-tree-inorder-traversal/ |

---

## 1. Input / Output
```
input  → root = [1,null,2,3]
output → [1,3,2]
```
Return node values in inorder (left → node → right).

## 2. Data structure used
- Recursion (call stack), or an explicit `stack` for the iterative form.

## 3. Why this approach works
**The order of three lines IS the traversal.** Inorder = `dfs(left) → visit → dfs(right)`.
(Move `visit` first → preorder; last → postorder — same three lines, three traversals.) For a
**BST**, inorder yields values in **sorted ascending order** — the property behind the Greater
Sum Tree and Median-Level re-drills.

**Iterative = the recursion made visible.** The explicit `stack` simulates the call stack:
dive left pushing every node; when you can't go left, pop (that's the leftmost / next-in-order
node), visit it, then pivot right and repeat. The stack holds "descended-past but not-yet-
visited" nodes — exactly what the call stack holds recursively.

## 4. Time / space complexity
- O(n) time, O(h) space (call stack or explicit stack).

---

## My solutions
```python
# 1 — recursive (the trivial version the follow-up dismisses)
def dfs(node):
    if not node: return
    dfs(node.left); result.append(node.val); dfs(node.right)

# 2 — iterative with explicit stack (dodges recursion-depth limit on a skewed tree)
res, stack, curr = [], [], root
while curr or stack:
    while curr: stack.append(curr); curr = curr.left
    curr = stack.pop(); res.append(curr.val); curr = curr.right
```

## What tripped me up
Nothing. The subtle correctness bit: the loop guard is `while curr or stack` — keep going while
there's a node to descend into *or* unvisited ancestors waiting. Either half alone breaks it.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | Owed — reproduce the iterative version cold |
| **Where I got stuck** | — |

## Interview sentence
> Inorder is left-node-right; recursively that's three lines. Iteratively I use an explicit
> stack that mimics the call stack — dive left pushing, pop-and-visit, pivot right — which I'd
> reach for when the tree could be deep enough to blow the recursion limit.
