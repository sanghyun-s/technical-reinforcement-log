# 938. Range Sum of BST

**Difficulty:** Easy  
**Pattern:** Trees / DFS / BST  
**Date Solved:** 2026-05-15  
**Status:** ✅

---

## Understanding the Goal

Sum all node values within range [low, high].

**Key insight:** Use BST property to prune branches - if node.val < low, skip left; if node.val > high, skip right.

---

## LAYER 1: Editorial Code Explained

### **Approach 1: Recursive DFS**

```python
def rangeSumBST(self, root: Optional[TreeNode], low: int, high: int) -> int:
    def dfs(node):
        nonlocal ans  # Access outer function's ans variable
        if node:
            # If node is in range, add to sum
            if low <= node.val <= high:
                ans += node.val
            
            # If node.val > low, left subtree might contain values >= low
            # (BST: left < node, so only check if node > low)
            if low < node.val:
                dfs(node.left)
            
            # If node.val < high, right subtree might contain values <= high
            # (BST: right > node, so only check if node < high)
            if node.val < high:
                dfs(node.right)
    
    ans = 0
    dfs(root)
    return ans
```

**Logic:**
- Add node if within range
- Go left only if node.val > low (left might be >= low)
- Go right only if node.val < high (right might be <= high)

### **Approach 2: Iterative (Stack)**

```python
def rangeSumBST(self, root: Optional[TreeNode], low: int, high: int) -> int:
    ans = 0
    stack = [root]  # Start with root
    
    while stack:
        node = stack.pop()  # Get next node to process
        
        if node:  # Only process non-null nodes
            # Add to sum if in range
            if low <= node.val <= high:
                ans += node.val
            
            # Push children based on BST pruning logic
            if low < node.val:
                stack.append(node.left)
            if node.val < high:
                stack.append(node.right)
    
    return ans
```

**Same logic, using explicit stack instead of recursion.**

---

## LAYER 2: Worked Examples

**Example 1: [10,5,15,3,7,null,18], low=7, high=15**

```
Recursive trace:
  dfs(10): 10 in [7,15]? YES → ans=10
    10 > 7? YES → dfs(5)
      5 in [7,15]? NO
      5 > 7? NO → skip left
      5 < 15? YES → dfs(7)
        7 in [7,15]? YES → ans=17
        7 > 7? NO → skip left
        7 < 15? YES → dfs(null) → return
    10 < 15? YES → dfs(15)
      15 in [7,15]? YES → ans=32
      15 > 7? YES → dfs(null) → return
      15 < 15? NO → skip right

Result: 32 ✓ (7+10+15)
```

**Example 2: [10,5,15,3,7,13,18,1,null,6], low=6, high=10**

```
Nodes in range [6,10]: 6, 7, 10
Sum: 6+7+10 = 23 ✓

Pruning saves time:
- Skip 5's subtree (nodes 3,1 < 6)
- Skip 15's subtree (nodes 13,18 > 10)
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(n) worst case, O(h) best case with pruning |
| **Space** | O(h) recursion stack (or explicit stack) |
| **Key pruning** | Skip entire branches based on BST property |
| **Why BST?** | Can eliminate branches without checking every node |

**Pruning examples:**
- Node=5, low=7 → left subtree all < 5, skip it
- Node=15, high=10 → right subtree all > 15, skip it

---

## LAYER 4: Interview Variations

• **If asked "non-BST tree"** → Must check every node (no pruning), O(n) always  
• **If asked "count nodes in range"** → Same logic, count instead of sum  
• **If asked "return nodes in range"** → Same logic, collect values in list instead  

---

## LAYER 5: Cheat Sheet

**Recursive:**
```python
def rangeSumBST(self, root, low, high):
    def dfs(node):
        if not node: return 0
        total = 0
        if low <= node.val <= high:
            total = node.val
        if low < node.val:
            total += dfs(node.left)
        if node.val < high:
            total += dfs(node.right)
        return total
    return dfs(root)
```

**Iterative:**
```python
def rangeSumBST(self, root, low, high):
    ans = 0
    stack = [root]
    while stack:
        node = stack.pop()
        if node:
            if low <= node.val <= high:
                ans += node.val
            if low < node.val:
                stack.append(node.left)
            if node.val < high:
                stack.append(node.right)
    return ans
```

---

**Time:** O(n) worst | O(h) best | **Space:** O(h)
