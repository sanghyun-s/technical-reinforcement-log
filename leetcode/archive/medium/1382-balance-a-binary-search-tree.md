# 1382. Balance a Binary Search Tree

**Difficulty:** Medium
**Pattern:** Tree / DFS / Inorder Traversal + Divide & Conquer Construction
**Date Solved:** 2026-06-05
**Status:** ✅

---

## Understanding the Goal

Take an unbalanced BST and rebuild it as a **balanced BST** with the same values. "Balanced" = at every node, height of left/right subtrees differ by at most 1. Multiple valid answers — return any.

**Key insight — the two-phase strategy:**

1. **Phase 1 (Flatten):** Inorder-traverse the BST → values in **sorted order**. (BST's superpower: inorder = sorted.)
2. **Phase 2 (Rebuild):** Recursively construct from the sorted array by picking the **middle** as root, then recursing on halves.

Why the middle? Splitting at the middle gives two equal halves → guaranteed balance. Same idea as binary search.

**Composition of patterns you already know:**
- Inorder traversal → LC 94
- Sorted array → balanced BST → LC 108 (literally just Phase 2)

This problem = LC 94 + LC 108. Recognizing this is the Medium-tier insight.

---

## LAYER 1: Line-by-Line Explanation

### Editorial style — three separate methods

```python
class Solution:
    def balanceBST(self, root: TreeNode) -> TreeNode:
        # Step 1: Flatten BST to sorted list via inorder
        inorder = []
        self.inorder_traversal(root, inorder)
        
        # Step 2: Build balanced BST from sorted list
        return self.create_balanced_bst(inorder, 0, len(inorder) - 1)
    
    def inorder_traversal(self, root: TreeNode, inorder: list):
        # Same skeleton as LC 94: Left → Node → Right produces sorted output for BST
        if not root:
            return
        self.inorder_traversal(root.left, inorder)    # 1. Drain left (smaller values)
        inorder.append(root.val)                       # 2. Record this node
        self.inorder_traversal(root.right, inorder)   # 3. Drain right (larger values)
    
    def create_balanced_bst(self, inorder: list, start: int, end: int) -> TreeNode:
        # Base case: empty range → no subtree
        if start > end:
            return None
        
        # Pick MIDDLE as root (guarantees balance)
        # start + (end - start) // 2 is overflow-safe form of (start + end) // 2
        mid = start + (end - start) // 2
        
        # Recursively build left subtree from LEFT half
        left_subtree = self.create_balanced_bst(inorder, start, mid - 1)
        
        # Recursively build right subtree from RIGHT half
        right_subtree = self.create_balanced_bst(inorder, mid + 1, end)
        
        # Create this subtree's root with middle value
        node = TreeNode(inorder[mid], left_subtree, right_subtree)
        return node
```

**Why "middle as root" works:**

- Middle has half the values on left → become left subtree
- Middle has half the values on right → become right subtree
- Each half balanced by same recursive logic
- Subtree size difference ≤ 1 → height difference ≤ 1 → balanced

**Why `start + (end - start) // 2` not `(start + end) // 2`:**

In Python doesn't matter (unbounded ints). In C++/Java, `start + end` could overflow. Memorize for cross-language interviews.

### Cleaner single-method version

```python
class Solution:
    def balanceBST(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        # Phase 1: Inorder collects values in sorted order
        sorted_vals = []
        def inorder(node):
            if not node:
                return
            inorder(node.left)
            sorted_vals.append(node.val)
            inorder(node.right)
        inorder(root)
        
        # Phase 2: Build balanced BST from sorted list
        def build(left, right):
            if left > right:
                return None
            mid = (left + right) // 2
            return TreeNode(sorted_vals[mid],
                            build(left, mid - 1),
                            build(mid + 1, right))
        
        return build(0, len(sorted_vals) - 1)
```

### Generator-based (super-Pythonic)

```python
class Solution:
    def balanceBST(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        def inorder(node):
            if node:
                yield from inorder(node.left)    # Yield left-subtree values first
                yield node.val                    # Then this node
                yield from inorder(node.right)    # Then right-subtree values
        
        vals = list(inorder(root))               # Generator → sorted list
        
        def build(l, r):
            if l > r:
                return None
            m = (l + r) // 2
            return TreeNode(vals[m], build(l, m - 1), build(m + 1, r))
        
        return build(0, len(vals) - 1)
```

---

## LAYER 2: Worked Examples

### Example 1: Right-skewed `[1, null, 2, null, 3, null, 4]` → balanced

**Before:**
```
1
 \
  2
   \
    3
     \
      4
```

**Phase 1 — inorder:** `vals = [1, 2, 3, 4]` (sorted ✓)

**Phase 2 — build:**

```
build(0, 3):
  mid = (0 + 3) // 2 = 1, value = vals[1] = 2
  left = build(0, 0): mid=0, value=1, no children → TreeNode(1)
  right = build(2, 3):
    mid = 2, value = vals[2] = 3
    left = build(2, 1) = None
    right = build(3, 3): mid=3, value=4 → TreeNode(4)
    → TreeNode(3, None, TreeNode(4))
  → TreeNode(2, TreeNode(1), TreeNode(3, None, TreeNode(4)))
```

**Result:**
```
        2
       / \
      1   3
           \
            4
```

Level-order: `[2, 1, 3, null, null, null, 4]` ✓

### Example 2: Already balanced `[2, 1, 3]` → `[2, 1, 3]`

Phase 1: `[1, 2, 3]`
Phase 2: mid=1, value=2; left=1, right=3 → same shape ✓

### Edge cases
- Single node: `vals = [v]`, build returns TreeNode(v)
- Two nodes: always balanced after rebuild
- Worst-case skew n=10⁴: O(n) recursion depth in Phase 1 → borderline Python limit

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Inorder + middle-build | O(n) | O(n) array + O(log n) recursion | **Standard** |
| Day-Stout-Warren | O(n) | O(1) | Advanced; in-place rotations |

**Two-phase pattern (worth internalizing):**

Many tree problems decompose into:
1. **Phase 1:** Extract data (traverse)
2. **Phase 2:** Rebuild or query (use extracted)

Examples:
- This problem (extract sorted → build balanced)
- LC 108 Sorted Array → BST (skip Phase 1, you have the array)
- LC 105 Build Tree from Preorder + Inorder
- LC 449 Serialize/Deserialize BST

When you see "restructure a tree," ask: "can I flatten and rebuild?"

**Why inorder gives sorted for BST:**

BST invariant: `left subtree < node < right subtree`. Inorder visits left FIRST (smaller), then node (middle), then right (larger). Always smallest-to-largest.

If problem says "binary tree" (not BST), you'd need to sort separately → O(n log n).

**Why "middle as root" guarantees balance:**

For array of length n:
- Left half: ⌊n/2⌋ elements
- Right half: ⌈n/2⌉ − 1 elements
- Difference: at most 1

Holds recursively → every node's subtrees differ by ≤ 1 → balanced.

**Connection to binary search:**

"Middle of sorted array → recursive halves" is exactly binary search's recursion tree. Depth: ⌈log₂(n+1)⌉ — optimal balance.

**Why Medium:**

Pattern composition. Beginners try to do it in one pass and get stuck. The "middle as root" insight non-obvious without LC 108 exposure.

**Connection to today's tree problems:**

| Problem | Phase | Builds on |
|---------|-------|-----------|
| LC 94 Inorder | Phase 1 only | — |
| LC 100 Same Tree | Single-phase parallel | LC 94 idea |
| LC 104 Max Depth | Single-phase aggregation | LC 94 idea |
| LC 226 Invert | Single-phase mutation | LC 94 idea |
| **LC 1382** | **Phase 1 (LC 94) + Phase 2 (LC 108)** | **Composes both** |

You've literally been building toward this.

---

## LAYER 4: Interview Variations

• **LC 108 Convert Sorted Array → BST:** Just Phase 2 of this problem.
• **LC 109 Convert Sorted Linked List → BST:** (1) Convert to array. (2) Inorder simulation trick: O(log n) space.
• **Balance binary tree (NOT BST):** Phase 1 gives unsorted; sort first → O(n log n).
• **Verify BST balanced (LC 110):** Recursive height check with -1 sentinel.
• **Self-balancing BSTs (AVL, Red-Black):** Different problem — maintain on insert/delete.
• **In-place balancing:** Day-Stout-Warren algorithm.
• **Custom selection rule (not middle):** Always-left → left chain; random → treap.
• **Min weighted path length:** Hu-Tucker algorithm.
• **Access-pattern-optimized BST:** Optimal BST construction (DP).
• **Reconstruct from preorder (LC 1008):** Bounds-based recursion.
• **Reconstruct from postorder:** Walk array reverse.
• **Inorder + preorder (LC 105):** Inorder splits at preorder-root.
• **Inorder + postorder (LC 106):** Postorder gives root from end.
• **Streaming balance:** Use AVL/Red-Black for live updates.
• **Verify output balanced:** Apply LC 110 check.
• **Min depth of result:** ⌈log₂(n+1)⌉.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Clean two-phase with nested helpers
class Solution:
    def balanceBST(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        # Phase 1: Inorder → sorted list
        sorted_vals = []
        def inorder(node):
            if not node:
                return
            inorder(node.left)
            sorted_vals.append(node.val)
            inorder(node.right)
        inorder(root)
        
        # Phase 2: Recursive build, middle-as-root
        def build(l, r):
            if l > r:
                return None
            mid = (l + r) // 2
            return TreeNode(sorted_vals[mid],
                            build(l, mid - 1),
                            build(mid + 1, r))
        
        return build(0, len(sorted_vals) - 1)
```

**Editorial — three methods:**
```python
class Solution:
    def balanceBST(self, root):
        inorder = []
        self.inorder_traversal(root, inorder)
        return self.create_balanced_bst(inorder, 0, len(inorder) - 1)
    
    def inorder_traversal(self, root, inorder):
        if not root: return
        self.inorder_traversal(root.left, inorder)
        inorder.append(root.val)
        self.inorder_traversal(root.right, inorder)
    
    def create_balanced_bst(self, inorder, start, end):
        if start > end: return None
        mid = start + (end - start) // 2
        return TreeNode(inorder[mid],
                        self.create_balanced_bst(inorder, start, mid - 1),
                        self.create_balanced_bst(inorder, mid + 1, end))
```

**Generator-based:**
```python
class Solution:
    def balanceBST(self, root):
        def inorder(node):
            if node:
                yield from inorder(node.left)
                yield node.val
                yield from inorder(node.right)
        
        vals = list(inorder(root))
        
        def build(l, r):
            if l > r: return None
            m = (l + r) // 2
            return TreeNode(vals[m], build(l, m - 1), build(m + 1, r))
        
        return build(0, len(vals) - 1)
```

**Iterative inorder (safer for very deep trees):**
```python
def balanceBST(root):
    vals = []
    stack = []
    curr = root
    while curr or stack:
        while curr:
            stack.append(curr)
            curr = curr.left
        curr = stack.pop()
        vals.append(curr.val)
        curr = curr.right
    
    def build(l, r):
        if l > r: return None
        m = (l + r) // 2
        return TreeNode(vals[m], build(l, m - 1), build(m + 1, r))
    
    return build(0, len(vals) - 1)
```

---

**Time:** O(n) | **Space:** O(n) array + O(log n) recursion

**Pattern flag:** "Restructure a tree" → consider **two-phase: flatten + rebuild**. For BSTs, **inorder gives sorted output** (no separate sort). The "middle of sorted array as root" construction is canonical for balanced BSTs in O(n) — same divide-and-conquer that powers binary search. Recognizing this problem = LC 94 + LC 108 is the Medium-tier insight.

---

## ⚠️ Common Pitfalls (from real debugging)

When typing this from memory, watch for:

1. **Method name consistency** — if you misspell the method definition (e.g., `creat_balanced_bst` instead of `create_balanced_bst`) but call it correctly, Python's helpful "Did you mean?" error suggests the misspelled definition. Always check both directions.

2. **`root.right` vs `root.left` in inorder** — easy to copy-paste line 1 to line 3 of inorder and forget to change `left` to `right`. Results: missing right-side values, wrong order, wrong final tree.

3. **Mid formula: `+` vs `-`** — overflow-safe is `start + (end - start) // 2`. Writing `(end + start)` accidentally works for some inputs but produces out-of-range indices for non-zero `start`, leading to `IndexError` or wrong tree.

When tests fail mysteriously, check these three before anything else.
