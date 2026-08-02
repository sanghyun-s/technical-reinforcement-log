# 226. Invert Binary Tree

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | Trees (DFS / BFS) |
| **Date** | 2026-07-30 |
| **Mode** | Reference Recall (re-drill of archived 226; recursive core hand-typed cold) |
| **Link** | https://leetcode.com/problems/invert-binary-tree/ |

---

## 1. Input / Output
```
input  → [4,2,7,1,3,6,9]
output → [4,7,2,9,6,3,1]
```
Mirror the tree: every node's two children swap.

## 2. Data structure used
- The tree + recursion (call stack), or an explicit `stack` (DFS) / `deque` (BFS).

## 3. Why this approach works
The whole operation is one local action — **swap each node's two children** — repeated over
every node. It's **order-independent**: no swap depends on any other node's state, so recursion,
a stack, or a queue all produce the same result. (Contrast 538, which *needed* reverse-inorder
because each node depended on larger values already summed. Knowing which problems have an
ordering constraint and which don't is the transferable skill.)

## 4. Time / space complexity
- Time: O(n) — every node visited once.
- Space: O(h) recursion / O(n) worst case for the stack or queue.

---

## My solutions (4 traversals, all correct)
```python
# 1 — recursion, capture then assign
def invertTree(self, root):
    if not root: return None
    right = self.invertTree(root.right)
    left  = self.invertTree(root.left)
    root.left, root.right = right, left
    return root

# 2 — recursion, tuple-swap one-liner (RHS fully evaluated before assignment → safe)
def invertTree(self, root):
    if not root: return None
    root.left, root.right = self.invertTree(root.right), self.invertTree(root.left)
    return root

# 3 — iterative DFS (stack / LIFO)
def invertTree(self, root):
    if not root: return None
    stack = [root]
    while stack:
        node = stack.pop()
        node.left, node.right = node.right, node.left
        if node.left:  stack.append(node.left)
        if node.right: stack.append(node.right)
    return root

# 4 — iterative BFS (deque / FIFO)  — identical to #3 but popleft()
```

## What tripped me up
Nothing broke. The insight to keep: **#3 and #4 differ by one data structure** — `stack.pop()`
(LIFO → DFS) vs `queue.popleft()` (FIFO → BFS). Same nodes, same swaps, different visit order,
same result. That pair is the template for "turn a DFS into a BFS": swap the stack for a deque.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | **Owed** — reproduce the stack-DFS and queue-BFS without the archive |
| **Where I got stuck** | — |

## Interview sentence
> Inverting a tree is just swapping every node's two children, and because the operation is
> order-independent it works the same recursively, with a stack, or with a queue — the DFS and
> BFS versions differ only in `pop()` vs `popleft()`.
