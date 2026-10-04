# 1038. Binary Search Tree to Greater Sum Tree

**Difficulty:** Medium  
**Pattern:** Trees / DFS / BST  
**Date Solved:** 2026-05-15  
**Status:** ✅

---

## Understanding the Goal

Convert BST: each node = original value + sum of all values greater than it.

---

## LAYER 1: Editorial Code Explained

```python
def bstToGst(self, root):
    # Container to store inorder (sorted) values
    self.inorder_traversal = []
    # Step 1: Get all values in sorted order (left→root→right)
    self.inorder(root)
    # Step 2: Reverse to descending (largest first)
    self.inorder_traversal.reverse()
    # Step 3: Update each node with sum of larger values
    self.replace_values(root)
    return root

def inorder(self, root):
    # Standard inorder traversal
    if root is None:
        return
    self.inorder(root.left)           # Process left subtree
    self.inorder_traversal.append(root.val)  # Add current
    self.inorder(root.right)          # Process right subtree

def replace_values(self, root):
    # Post-order: process children, then parent
    if root is None:
        return
    self.replace_values(root.left)
    self.replace_values(root.right)
    
    # Calculate sum of all values > current value
    node_sum = 0
    for i in self.inorder_traversal:
        if i > root.val:
            node_sum += i
        else:
            break  # Early exit (array is sorted descending)
    
    # Update: new value = old value + sum of larger values
    root.val += node_sum
```

---

## LAYER 2: Worked Example

**Input: [4,1,6,0,2,5,7,null,null,3,null,null,null,null,8]**

```
Step 1 - Inorder: [0, 1, 2, 3, 4, 5, 6, 7, 8]
Step 2 - Reverse: [8, 7, 6, 5, 4, 3, 2, 1, 0]
Step 3 - Replace:
  Node 0: Sum of values > 0 = 36 → 0+36 = 36
  Node 1: Sum of values > 1 = 35 → 1+35 = 36
  Node 2: Sum of values > 2 = 33 → 2+33 = 35
  Node 3: Sum of values > 3 = 30 → 3+30 = 33
  Node 4: Sum of values > 4 = 26 → 4+26 = 30 ✓
  Node 5: Sum of values > 5 = 21 → 5+21 = 26
  Node 6: Sum of values > 6 = 15 → 6+15 = 21
  Node 7: Sum of values > 7 = 8 → 7+8 = 15
  Node 8: Sum of values > 8 = 0 → 8+0 = 8

Output: [30,36,21,36,35,26,15,null,null,33,null,null,null,null,8] ✓
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(n²) - inorder O(n) + replace O(n²) from inner loop |
| **Space** | O(n) - inorder array storage |
| **Why reverse?** | Makes finding "larger values" simple (just loop until break) |
| **Why two passes?** | Clear separation: collect data → use data |

---

## LAYER 4: Interview Variations

• **If asked "do it in one pass"** → Use reverse inorder DFS (accumulate sum during traversal)  
• **If asked "what if not a BST"** → Must collect all values, can't optimize with BST property  
• **If asked "iterative instead of recursive"** → Use explicit stack for DFS  

---

## LAYER 5: Cheat Sheet

```python
def bstToGst(self, root):
    self.inorder_traversal = []
    self.inorder(root)
    self.inorder_traversal.reverse()
    self.replace_values(root)
    return root

def inorder(self, root):
    if not root: return
    self.inorder(root.left)
    self.inorder_traversal.append(root.val)
    self.inorder(root.right)

def replace_values(self, root):
    if not root: return
    self.replace_values(root.left)
    self.replace_values(root.right)
    node_sum = sum(i for i in self.inorder_traversal if i > root.val)
    root.val += node_sum
```

---

**Time:** O(n²) | **Space:** O(n)
