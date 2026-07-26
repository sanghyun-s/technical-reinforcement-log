# 894. All Possible Full Binary Trees

**Difficulty:** Medium
**Pattern:** Dynamic Programming / Recursion / Tree Construction / Catalan Numbers
**Date Solved:** 2026-06-09
**Status:** ✅

---

## Understanding the Goal

Generate **every possible full binary tree** with exactly `n` nodes. A full binary tree means every node has either 0 or 2 children (never just 1).

**Three observations:**

1. **Parity:** Full binary trees ALWAYS have an odd number of nodes. (1 root + each internal node contributes 2 children → total nodes = 1 + 2 × (internal) = odd.) If `n` is even, return `[]`.

2. **Base case:** `n = 1` → one tree (a single leaf).

3. **Recursive structure:** For `n ≥ 3`, root has LEFT subtree (i nodes) and RIGHT subtree (n-1-i nodes), where `i` is odd. For each combination, create new root and combine.

This is **divide-and-conquer on a structural property** — same skeleton as LC 1382 (Balance BST), but here we **enumerate all** instead of constructing one.

Also your **first encounter with Catalan numbers** — the count of full binary trees follows the Catalan sequence.

---

## LAYER 1: Line-by-Line Explanation

### Editorial — recursive enumeration

```python
class Solution:
    def allPossibleFBT(self, n: int) -> List[Optional[TreeNode]]:
        # Parity check: FBTs have odd node counts only
        if n % 2 == 0:
            return []
        
        # Base case: single leaf
        if n == 1:
            return [TreeNode()]
        
        res = []
        
        # Try every odd split of remaining n-1 nodes (excluding root)
        # Left gets `i` nodes (odd), right gets `n-1-i` (also odd)
        # i = 1, 3, 5, ..., n-2
        for i in range(1, n, 2):
            left = self.allPossibleFBT(i)           # All possible left subtrees
            right = self.allPossibleFBT(n - i - 1)  # All possible right subtrees
            
            # Cartesian product: every (left × right) gives a new tree
            for l in left:
                for r in right:
                    root = TreeNode(0, l, r)
                    res.append(root)
        
        return res
```

**Why `range(1, n, 2)`:**

Yields 1, 3, 5, ..., n-2 — all ODD values < n. Both subtrees must be FBTs (odd sizes). If left has odd `i`, right has `n - 1 - i` = even − odd = odd. ✓

**Sharing nodes:** Editorial reuses TreeNode references — multiple parents point to SAME child object. Fine because LeetCode serializes by structure. For strict identity, use `copy.deepcopy`.

### With memoization (massive speedup)

```python
from functools import lru_cache

class Solution:
    def allPossibleFBT(self, n: int) -> List[Optional[TreeNode]]:
        @lru_cache(maxsize=None)
        def build(k: int):
            if k % 2 == 0:
                return []
            if k == 1:
                return [TreeNode()]
            
            res = []
            for i in range(1, k, 2):
                for l in build(i):
                    for r in build(k - i - 1):
                        res.append(TreeNode(0, l, r))
            return res
        
        return build(n)
```

**Why memoization helps:** Without it, `build(5)` triggers `build(3)`, `build(1)`. Then `build(7)` re-triggers all of those. Memoization makes each `build(k)` run ONCE.

### Iterative (bottom-up) DP

```python
class Solution:
    def allPossibleFBT(self, n: int) -> List[Optional[TreeNode]]:
        if n % 2 == 0:
            return []
        
        # dp[k] = list of all FBTs with k nodes
        dp = {1: [TreeNode()]}
        
        for k in range(3, n + 1, 2):                # 3, 5, 7, ..., n
            dp[k] = []
            for i in range(1, k, 2):
                for l in dp[i]:
                    for r in dp[k - i - 1]:
                        dp[k].append(TreeNode(0, l, r))
        
        return dp[n]
```

Builds smallest-to-largest. No recursion stack. Equivalent to "tabulation."

---

## LAYER 2: Worked Examples

### Example 1: `n = 1` → 1 tree

`[TreeNode(0)]` — a single leaf. ✓

### Example 2: `n = 3` → 1 tree

Loop: `i = 1` only.
- `left = build(1) = [leaf]`
- `right = build(1) = [leaf]`
- Build: `[root(0, leaf, leaf)]` ✓

### Example 3: `n = 5` → 2 trees

Loop: `i = 1, 3`.

**i = 1:** left=1 node, right=3 nodes.
- `build(1) = [leaf]`, `build(3) = [small tree]`
- 1 × 1 = 1 tree

**i = 3:** left=3 nodes, right=1 node.
- `build(3) = [small tree]`, `build(1) = [leaf]`
- 1 × 1 = 1 tree

Total: 2 trees ✓ — mirror images.

### Example 4: `n = 7` → 5 trees

FBT counts follow **Catalan numbers**: 1, 1, 2, 5, 14, 42, ... for n = 1, 3, 5, 7, 9, 11, ...

For n=7, k=3 → C_3 = **5 trees** ✓

The 5 splits:
- (1, 5): 1 × 2 = 2 trees
- (3, 3): 1 × 1 = 1 tree
- (5, 1): 2 × 1 = 2 trees

Total: **5** ✓

### Edge cases

- **n = 0:** even → `[]`
- **n = 1:** single leaf
- **n = 2:** even → `[]`
- **All even n:** `[]`
- **n = 20 (max):** even → `[]`. Actual max odd input is 19 → 4862 trees.

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Naive recursion | O(2ⁿ) approx | O(n) recursion | Re-computes subproblems |
| **Memoized recursion** | **O(Catalan_n × n)** | O(n × Catalan_n) | **Standard** |
| Bottom-up iterative | Same | Same | No recursion stack |

**Catalan number connection:**

Number of FBTs with `2k+1` nodes = k-th Catalan number:

```
C_k = (1/(k+1)) × C(2k, k)
```

| nodes | k | trees |
|-------|---|-------|
| 1 | 0 | 1 |
| 3 | 1 | 1 |
| 5 | 2 | 2 |
| 7 | 3 | 5 |
| 9 | 4 | 14 |
| 11 | 5 | 42 |
| 13 | 6 | 132 |
| 19 | 9 | 4862 |

**Catalan numbers appear in:**
- Binary trees with n nodes (LC 96)
- Valid parentheses sequences (LC 22)
- Mountain ranges
- Triangulations of (n+2)-gon

If a counting sequence starts 1, 2, 5, 14, 42, 132 — think Catalan.

**"Enumerate all structures" pattern:**

> "Generate all possible X" → recursive construction; combine sub-structures.

Skeleton:
```python
def generate(size):
    if base_case: return [base_object]
    
    results = []
    for split_point in valid_splits(size):
        for left in generate(left_size):
            for right in generate(right_size):
                results.append(combine(left, right))
    return results
```

Applied in:
- LC 894 (this) — FBTs
- LC 95 Unique BSTs II — BST enumeration with values
- LC 241 Different Ways to Add Parentheses — operator placement
- Any "build all X" problem

**Why this is DP, not "just" recursion:**

DP = recursion + memoization. Naive recursion has overlapping subproblems — memoizing turns exponential into polynomial.

Three lenses:
- **Recursive structure:** divide root, recurse on subtrees
- **DP overlapping subproblems:** same subtree sizes recur
- **Combinatorial enumeration:** Cartesian product of sub-shapes

**Connection to LC 1382 (Balance BST):**

| Feature | LC 1382 | LC 894 |
|---------|---------|--------|
| Goal | Build ONE balanced BST | Build ALL FBTs |
| Output | Single tree | List of trees |
| Recursion | Pick middle as root | Try every split |
| Combination | l, r = build halves | for l in lefts, for r in rights |

Same skeleton; different branch factor.

---

## LAYER 4: Interview Variations

• **Count FBTs (no construction):** Catalan directly. O(n) DP on count.
• **Unique BSTs II (LC 95):** Same skeleton with VALUES — recurse on ranges.
• **Different Ways to Add Parentheses (LC 241):** Split at each operator.
• **All complete binary trees:** Different constraint — usually exactly 1 shape per n.
• **All BST shapes (count only):** Catalan again.
• **FBTs with bounded depth:** Add depth parameter.
• **FBTs with leaf values:** Extend with value lists.
• **Largest FBT in n nodes:** Pick largest odd k ≤ n.
• **Symmetric FBTs only:** Filter at construction.
• **Generators (lazy):** Yield trees to avoid memory explosion for large n.
• **Serialize all distinct FBTs:** Generate then call serialize on each.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Editorial style
class Solution:
    def allPossibleFBT(self, n: int) -> List[Optional[TreeNode]]:
        if n % 2 == 0:
            return []
        if n == 1:
            return [TreeNode()]
        
        res = []
        for i in range(1, n, 2):
            left = self.allPossibleFBT(i)
            right = self.allPossibleFBT(n - i - 1)
            for l in left:
                for r in right:
                    res.append(TreeNode(0, l, r))
        return res
```

**With memoization:**
```python
from functools import lru_cache

class Solution:
    def allPossibleFBT(self, n: int) -> List[Optional[TreeNode]]:
        @lru_cache(maxsize=None)
        def build(k):
            if k % 2 == 0:
                return []
            if k == 1:
                return [TreeNode()]
            res = []
            for i in range(1, k, 2):
                for l in build(i):
                    for r in build(k - i - 1):
                        res.append(TreeNode(0, l, r))
            return res
        return build(n)
```

**Bottom-up DP:**
```python
class Solution:
    def allPossibleFBT(self, n):
        if n % 2 == 0:
            return []
        dp = {1: [TreeNode()]}
        for k in range(3, n + 1, 2):
            dp[k] = []
            for i in range(1, k, 2):
                for l in dp[i]:
                    for r in dp[k - i - 1]:
                        dp[k].append(TreeNode(0, l, r))
        return dp[n]
```

**Compressed list comprehension:**
```python
class Solution:
    def allPossibleFBT(self, n):
        if n % 2 == 0:
            return []
        if n == 1:
            return [TreeNode()]
        return [
            TreeNode(0, l, r)
            for i in range(1, n, 2)
            for l in self.allPossibleFBT(i)
            for r in self.allPossibleFBT(n - i - 1)
        ]
```

**Count-only (Catalan without building):**
```python
def countFBT(n):
    if n % 2 == 0:
        return 0
    dp = [0] * (n + 1)
    dp[1] = 1
    for k in range(3, n + 1, 2):
        for i in range(1, k, 2):
            dp[k] += dp[i] * dp[k - i - 1]
    return dp[n]
```

---

**Time:** O(Catalan_(n/2) × n) ≈ O(4ⁿ / n^(3/2)) | **Space:** O(Catalan_(n/2)) for output

**Pattern flag:** "Generate all possible structures of size n" → **recursive divide-and-conquer** with **Cartesian product** of sub-structures. Three signals:
1. **Structural property** (FBT) suggests recursive composition
2. **Parity/size constraint** (n odd) gates base case
3. **Catalan sequence** (1, 1, 2, 5, 14, 42, ...) is a strong hint

The skeleton:
```python
def build(size):
    if base: return [base_object]
    return [combine(l, r)
            for split in valid_splits(size)
            for l in build(left_part(split))
            for r in build(right_part(split))]
```

Memoize on `size` → exponential becomes Catalan-bounded polynomial. Same pattern powers LC 95, LC 241, many others.

⭐ **First Catalan-counting problem** on the log.
