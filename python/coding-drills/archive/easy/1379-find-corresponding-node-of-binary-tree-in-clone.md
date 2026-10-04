# 1379. Find a Corresponding Node of a Binary Tree in a Clone of That Tree

**Difficulty:** Easy  
**Pattern:** Trees / DFS  
**Date Solved:** 2026-05-15  
**Status:** ✅

---

## Understanding the Goal

Find the node in `cloned` tree that corresponds to `target` node in `original` tree.

**Key insight:** Traverse both trees in parallel. When you find the target in original, return the corresponding node from cloned.

---

## LAYER 1: Code Solution

```python
def getTargetCopy(self, original: TreeNode, cloned: TreeNode, target: TreeNode) -> TreeNode:
    # Base case: if original is None, cloned is also None
    if not original:
        return None
    
    # Check if current original node is the target
    # Use == for value comparison (works even with repeated values)
    if original.val == target.val:
        # Found it! Return the corresponding cloned node
        return cloned
    
    # If not found, search in left subtree
    left_result = self.getTargetCopy(original.left, cloned.left, target)
    if left_result:
        return left_result
    
    # If not found in left, search in right subtree
    right_result = self.getTargetCopy(original.right, cloned.right, target)
    return right_result
```

**Logic:**
1. Use `original.val == target.val` (value check, works with duplicates)
2. When found, return corresponding `cloned` node
3. Search left, then right (pre-order)
4. Return first match found

---

## LAYER 2: Worked Examples

**Example 1: target=3 (node with value 3)**

```
original tree:      cloned tree:
    7                   7
   / \                 / \
  4   3               4   3
 /   / \             /   / \
    6   19              6   19

DFS on original:
  7.val == 3? No
    4.val == 3? No
      null
    3.val == 3? YES! Return cloned.3 ✓

Output: cloned node with value 3
```

**Example 2: target=7 (root)**

```
original tree:      cloned tree:
    7                   7

DFS on original:
  7.val == 7? YES! Return cloned.7 ✓

Output: cloned node with value 7
```

**Example 3: target=4 (in a chain)**

```
original:           cloned:
8                   8
 \                   \
  6                   6
   \                   \
    5                   5
     \                   \
      4                   4
     /                   /
    3                   3
     \                   \
      2                   2
       \                   \
        1                   1

DFS on original:
  8.val == 4? No
    null (no left)
    6.val == 4? No
      null
      5.val == 4? No
        null
        4.val == 4? YES! Return cloned.4 ✓

Output: cloned node with value 4
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(n) worst case - may visit all nodes |
| **Space** | O(h) - recursion depth |
| **Key check** | `original.val == target.val` (value, not identity) |
| **Parallel traversal** | Both trees traversed together |
| **Return immediately** | As soon as target found |

**Why `==` not `is`?**
- `is` checks object identity (memory reference)
- `==` checks value equality
- Using `==` handles repeated values correctly

---

## LAYER 4: Interview Variations

• **If unique values guaranteed** → Can use `original is target` (identity check)  
• **If asked for iterative** → Use queue for BFS instead of recursion  
• **If asked to verify clone** → Check both nodes match before returning  

---

## LAYER 5: Cheat Sheet

```python
def getTargetCopy(self, original, cloned, target):
    if not original:
        return None
    
    if original.val == target.val:
        return cloned
    
    left = self.getTargetCopy(original.left, cloned.left, target)
    if left:
        return left
    
    return self.getTargetCopy(original.right, cloned.right, target)
```

**Key:** Traverse in parallel, use `==` for value comparison (handles duplicates).

**Alternative (unique values only):**
```python
def getTargetCopy(self, original, cloned, target):
    if not original:
        return None
    
    if original is target:  # Use is for identity check
        return cloned
    
    left = self.getTargetCopy(original.left, cloned.left, target)
    if left:
        return left
    
    return self.getTargetCopy(original.right, cloned.right, target)
```

---

**Time:** O(n) | **Space:** O(h)
