# 2265. Count Nodes Equal to Average of Subtree

**Difficulty:** Medium  
**Pattern:** Trees / DFS / Post-order  
**Date Solved:** 2026-05-15  
**Status:** ✅

---

## Understanding the Goal

Count nodes where: **node.val == floor(subtree_sum / subtree_count)**

**Key insight:** Need both sum AND count of each subtree. Use post-order DFS to return (sum, count) for each subtree.

---

## LAYER 1: Code Solution

```python
def averageOfSubtree(self, root: TreeNode) -> int:
    self.count = 0
    
    def dfs(node):
        # Returns (sum_of_subtree, count_of_nodes_in_subtree)
        if not node:
            return (0, 0)
        
        # Get left subtree info
        left_sum, left_count = dfs(node.left)
        
        # Get right subtree info
        right_sum, right_count = dfs(node.right)
        
        # Calculate current subtree
        subtree_sum = node.val + left_sum + right_sum
        subtree_count = 1 + left_count + right_count
        
        # Check if current node equals its subtree average
        if node.val == subtree_sum // subtree_count:
            self.count += 1
        
        # Return info for parent to use
        return (subtree_sum, subtree_count)
    
    dfs(root)
    return self.count
```

**Logic:**
1. Post-order: process left → right → current
2. From children, get (sum, count) of their subtrees
3. Calculate current subtree's (sum, count)
4. Check if node.val == average (sum // count)
5. Return (sum, count) to parent

---

## LAYER 2: Worked Example

**Example 1: [4,8,5,0,1,null,6]**

```
Post-order traversal:

Node 0 (leaf):
  left=(0,0), right=(0,0)
  sum=0, count=1
  avg=0//1=0 ✓ Match! count=1
  Return (0,1)

Node 1 (leaf):
  left=(0,0), right=(0,0)
  sum=1, count=1
  avg=1//1=1 ✓ Match! count=2
  Return (1,1)

Node 8:
  left=(0,1), right=(1,1)
  sum=8+0+1=9, count=1+1+1=3
  avg=9//3=3 ✗ No match
  Return (9,3)

Node 6 (leaf):
  sum=6, count=1
  avg=6//1=6 ✓ Match! count=3
  Return (6,1)

Node 5:
  left=(0,0), right=(6,1)
  sum=5+0+6=11, count=1+0+1=2
  avg=11//2=5 ✓ Match! count=4
  Return (11,2)

Node 4 (root):
  left=(9,3), right=(11,2)
  sum=4+9+11=24, count=1+3+2=6
  avg=24//6=4 ✓ Match! count=5
  Return (24,6)

Result: 5 ✓ (nodes 0,1,6,5,4)
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(n) - visit each node once |
| **Space** | O(h) - recursion depth |
| **Post-order** | Must process children before parent |
| **Return value** | (sum, count) tuple for parent |
| **Floor division** | Use // to round down average |

**Why post-order?**
- Need children's (sum, count) before calculating parent's
- Natural bottom-up approach

---

## LAYER 4: Interview Variations

• **If asked "return nodes that match"** → Collect nodes instead of counting  
• **If asked "average not rounded"** → Use `sum / count` and check float equality (careful with precision)  
• **If asked "node.val < average"** → Change condition to `node.val < subtree_sum // subtree_count`  

---

## LAYER 5: Cheat Sheet

```python
def averageOfSubtree(self, root):
    self.count = 0
    
    def dfs(node):
        if not node:
            return (0, 0)
        
        left_sum, left_count = dfs(node.left)
        right_sum, right_count = dfs(node.right)
        
        total_sum = node.val + left_sum + right_sum
        total_count = 1 + left_count + right_count
        
        if node.val == total_sum // total_count:
            self.count += 1
        
        return (total_sum, total_count)
    
    dfs(root)
    return self.count
```

**Key:** Return tuple (sum, count) from each subtree.

---

**Time:** O(n) | **Space:** O(h)
