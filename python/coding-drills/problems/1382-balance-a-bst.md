# 1382. Balance a Binary Search Tree

| | |
|---|---|
| **Difficulty** | Medium |
| **Family** | Trees / BST (inorder + build) |
| **Date** | 2026-08-19 |
| **Mode** | Reference Recall (A1 submitted, A2/A3 hand-typed from archive) |
| **Link** | https://leetcode.com/problems/balance-a-binary-search-tree/ |

---

## 1. Problem
Given a BST, return a **balanced** BST with the same values (depths of every node's two
subtrees differ by ≤ 1).

## 2. The idea — a two-phase composition
Rebalancing sounds hard (rotations / AVL), but it decomposes into two techniques already
drilled:
- **Phase 1 — inorder → sorted array.** Inorder on a BST emits ascending values for free
  (the 94/538 lesson). A right-leaning `[1→2→3→4]` flattens to `[1,2,3,4]`.
- **Phase 2 — sorted array → balanced BST by always picking the middle as root.** `mid` is the
  root; recurse the left half → left subtree, right half → right subtree. The always-pick-middle
  rule guarantees each subtree's two sides differ by ≤ 1 node — **exactly the balance definition.**

This is **binary search's midpoint logic run backwards** — same `mid = (lo+hi)//2`,
divide-and-conquer, but *building* a tree instead of *searching* an array.

## 3. Complexity
O(n) time (inorder touches each node once, build creates each once), O(n) space.

---

## My solutions (one algorithm, three idioms)
```python
# A2 — nested closures (idiomatic submit)
def balanceBST(self, root):
    vals = []
    def inorder(n):
        if not n: return
        inorder(n.left); vals.append(n.val); inorder(n.right)
    inorder(root)
    def build(lo, hi):
        if lo > hi: return None
        mid = (lo + hi) // 2
        return TreeNode(vals[mid], build(lo, mid-1), build(mid+1, hi))
    return build(0, len(vals) - 1)

# A1 — helper methods on self; uses overflow-safe start + (end-start)//2 (testable in isolation)
# A3 — generator: `yield from inorder(node.left)` streams values lazily, list() materialises
```

## Notes
- **Overflow-safe midpoint** (`lo + (hi-lo)//2`, A1) vs plain `(lo+hi)//2` (A2/A3): cosmetic in
  Python (arbitrary-precision ints), but the plain form is the classic binary-search overflow bug
  in fixed-width languages (Java/C++). Worth being able to explain.
- **`yield from`** (A3) is the Pythonic way to recursively flatten a tree into a value stream.
- A1's split into named methods is the most *testable* structure (unit-test `build` alone) — ties
  to the Testing course.

## Interview sentence
> I flatten the BST to a sorted array with an inorder traversal, then rebuild by recursively
> making the middle element the root — left half becomes the left subtree, right half the right.
> The middle-element choice guarantees the depth difference stays within one. O(n) — really binary
> search's midpoint logic run backwards to build instead of search.
