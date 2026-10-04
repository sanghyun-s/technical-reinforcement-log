# 94. Binary Tree Inorder Traversal

**Difficulty:** Easy
**Pattern:** Tree / Depth-First Search (DFS) / Iterative Stack Simulation
**Date Solved:** 2026-06-05
**Status:** ✅

---

## Understanding the Goal

Visit every node of a binary tree in **inorder** sequence: **Left subtree → Current node → Right subtree**.

**TreeNode object structure:**
- `node.val` — integer stored here
- `node.left` — left child (or `None`)
- `node.right` — right child (or `None`)

The `[1, null, 2, 3]` notation is LeetCode's level-order serialization — describes the input, not how you access it.

**Three traversals (memorize):**

| Traversal | Order |
|-----------|-------|
| **Inorder** | Left → Node → Right |
| Preorder | Node → Left → Right |
| Postorder | Left → Right → Node |

**For BSTs, inorder gives sorted ascending output.** Common interview test.

Two solution shapes:
1. **Recursive** — short, elegant, uses call stack implicitly
2. **Iterative with explicit stack** — interview-impressive, mirrors what recursion does

---

## LAYER 1: Line-by-Line Explanation

### Recursive — cleanest

```python
class Solution:
    def inorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        result = []                                 # Output list, in inorder
        
        def dfs(node):                              # Helper: recurse on a subtree
            if not node:                            # Base case: empty subtree
                return
            dfs(node.left)                          # 1️⃣ FIRST visit entire left subtree
            result.append(node.val)                 # 2️⃣ THEN record this node's value
            dfs(node.right)                         # 3️⃣ FINALLY visit entire right subtree
        
        dfs(root)
        return result
```

**Reading the recursion:**
- Three steps enforce inorder: left fully processed BEFORE current node, right AFTER
- Base case handles null children automatically
- `result` is captured by closure — all calls append to same list

### Iterative — editorial's stack-based version

```python
class Solution:
    def inorderTraversal(self, root):
        res = []                                    # Output list
        stack = []                                  # Stack of nodes whose left subtree we're processing
        curr = root                                 # Cursor: node currently examined
        
        while curr or stack:                        # Continue while work remains
            # Phase 1: dive as far LEFT as possible, pushing each node
            while curr:
                stack.append(curr)                  # Remember — we'll process later
                curr = curr.left                    # Go further left
            
            # Phase 2: leftmost reached. Pop, process, pivot RIGHT
            curr = stack.pop()                      # Deepest unprocessed left-ancestor
            res.append(curr.val)                    # Record value (the "node" step)
            curr = curr.right                       # Switch to right subtree
        
        return res
```

**Two-phase logic:**

- **Phase 1 (inner while):** "Go as far left as you can, remembering everyone." Mirrors recursion descending left, building stack.
- **Phase 2:** "Pop the most recently-skipped node, process it, then pivot right."

**Why `while curr or stack`:** After processing and moving to `curr.right`:
- If right child exists, `curr` non-None → Phase 1 dives left into right subtree
- If right child is None, `curr` is None → if stack non-empty, immediately pop again

### Why TWO loops?

Two-loop structure cleanly separates:
- **Inner loop:** go deep left (the "left subtree" part of inorder)
- **Outer loop body after inner:** process node, then go right

Recursion hides this by letting call stack bookkeep.

---

## LAYER 2: Worked Examples

### Example 1: `root = [1, null, 2, 3]`

Tree:
```
    1
     \
      2
     /
    3
```

**Recursive trace** of `dfs(n1)`:

```
dfs(n1):
  dfs(n1.left = None) → returns
  result.append(1) → [1]
  dfs(n1.right = n2):
    dfs(n2.left = n3):
      dfs(n3.left = None) → returns
      result.append(3) → [1, 3]
      dfs(n3.right = None) → returns
    result.append(2) → [1, 3, 2]
    dfs(n2.right = None) → returns
```

Final: `[1, 3, 2]` ✓

**Iterative trace:**

| step | curr | stack | action | res |
|------|------|-------|--------|-----|
| start | n1 | [] | enter outer loop | [] |
| inner | n1 | [] | push n1, curr=None | [] |
| exit inner | None | [n1] | pop→n1, append 1 | [1] |
| pivot right | n2 | [] | curr = n1.right | [1] |
| inner | n2 | [] | push n2, curr=n3 | [1] |
| inner | n3 | [n2] | push n3, curr=None | [1] |
| exit inner | None | [n2, n3] | pop→n3, append 3 | [1, 3] |
| pivot right | None | [n2] | curr = None | [1, 3] |
| skip inner | None | [n2] | pop→n2, append 2 | [1, 3, 2] |
| pivot right | None | [] | curr = None | [1, 3, 2] |
| exit | — | [] | both empty | [1, 3, 2] |

### Example 2: Larger tree → `[4, 2, 6, 5, 7, 1, 3, 9, 8]`

Inorder visits leftmost-first conceptually:
- Dive into 2's subtree → 4 → record 4 → back to 2 → record 2
- Right of 2 = 5's subtree → 6 → record 6 → 5 → record 5 → 7 → record 7
- Back to 1 → record 1 → right = 3 → record 3 → right = 8 → 9 → record 9 → 8 → record 8

### Edge cases

- **Empty (`root=None`):** Returns `[]`
- **Single node:** Returns `[node.val]`
- **Left-skewed `[1,2,3,4]` all left:** Output reversed
- **Right-skewed:** Output in original order
- **BST:** Sorted ascending output

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Recursive DFS | O(n) | O(h) call stack | **Cleanest** |
| Iterative w/ stack | O(n) | O(h) stack | **Interview-grade** |
| Morris traversal | O(n) | O(1) | Advanced; mutates tree |

n = node count, h = height.

**Three traversals through "when is node recorded":**

```python
def preorder(node):       def inorder(node):        def postorder(node):
    if not node: return       if not node: return       if not node: return
    visit(node)               inorder(node.left)        postorder(node.left)
    preorder(node.left)       visit(node)               postorder(node.right)
    preorder(node.right)      inorder(node.right)       visit(node)
```

Same skeleton, only `visit(node)` position changes.

**When recursive is right:**
- Tree depth bounded (n ≤ 100 here → depth ≤ 100, fine)
- Code clarity matters
- Just exploring tree

**When iterative is right:**
- Very deep trees (Python stack limit)
- Streaming traversal (yield one at a time)
- Interview wants you to show understanding
- Pause traversal mid-way (e.g., LC 173 BST Iterator)

**The "iterator pattern" connection:**

For "implement `hasNext()` and `next()` for inorder," you CAN'T use recursion. Need explicit stack to pause and resume.

**Conceptual bridge — recursion ↔ iteration with explicit stack:**

All recursion converts to iteration with explicit stack and vice versa. Every `stack.append(curr)` = function call; every `stack.pop()` = return.

---

## LAYER 4: Interview Variations

• **Preorder:** Move `visit(node)` before left recurse. Iterative: push right then left.
• **Postorder:** Tricky iteratively; do "modified preorder" (Node → Right → Left), then reverse.
• **Level-order (BFS):** Use queue, dequeue, visit, enqueue children.
• **Right-side view (LC 199):** Level-order, keep last node per level.
• **Validate BST (LC 98):** Inorder; check strictly increasing.
• **Kth smallest in BST (LC 230):** Inorder; return kth value.
• **Convert sorted array → BST (LC 108):** Recursive split at middle.
• **Reconstruct from preorder + inorder (LC 105):** Divide-and-conquer.
• **Morris traversal:** O(1) space via threading.
• **Iterative deepening DFS:** Combines DFS clarity with BFS level discovery.
• **Inorder iterator class (LC 173):** Wrap iterative version with `hasNext`/`next`.
• **N-ary tree inorder:** "Inorder" not fully defined for N-ary.
• **Tree with cycles:** Need visited set (graph DFS).
• **With parent pointers:** Traverse without any stack.
• **Threaded binary tree:** O(1)-space inorder native.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Recursive (cleanest)
class Solution:
    def inorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        result = []
        def dfs(node):
            if not node:
                return
            dfs(node.left)        # 1. Left subtree
            result.append(node.val)  # 2. Current node
            dfs(node.right)       # 3. Right subtree
        dfs(root)
        return result
```

**Iterative with explicit stack:**
```python
def inorderTraversal(root):
    res = []
    stack = []
    curr = root
    while curr or stack:
        while curr:
            stack.append(curr)
            curr = curr.left
        curr = stack.pop()
        res.append(curr.val)
        curr = curr.right
    return res
```

**One-line recursive (less efficient — list concat is O(n²) worst case):**
```python
def inorderTraversal(root):
    return (inorderTraversal(root.left) + [root.val] + inorderTraversal(root.right)) if root else []
```

**Generator (memory-efficient streaming):**
```python
def inorder_gen(node):
    if node:
        yield from inorder_gen(node.left)
        yield node.val
        yield from inorder_gen(node.right)

list(inorder_gen(root))
```

**Morris traversal (O(1) space — advanced):**
```python
def inorderTraversal(root):
    res = []
    curr = root
    while curr:
        if not curr.left:
            res.append(curr.val)
            curr = curr.right
        else:
            pre = curr.left
            while pre.right and pre.right != curr:
                pre = pre.right
            if not pre.right:
                pre.right = curr           # Create thread
                curr = curr.left
            else:
                pre.right = None            # Remove thread
                res.append(curr.val)
                curr = curr.right
    return res
```

---

**Time:** O(n) | **Space:** O(h)

**Pattern flag:** "Visit every node in specific order" → DFS with three orderings (pre/in/post). **Recursive** is the cleanest first answer. **Iterative with explicit stack** demonstrates recursion mastery — required for tree iterators and very deep trees. Mental model: **the explicit stack IS the call stack.** Every push = enter function; every pop = return.
