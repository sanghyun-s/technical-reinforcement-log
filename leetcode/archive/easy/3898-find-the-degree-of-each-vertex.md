# 3898. Find the Degree of Each Vertex

**Difficulty:** Easy
**Pattern:** Graph / Adjacency Matrix / Row Sum
**Date Solved:** 2026-06-10
**Status:** ✅

🎯 **First Graph problem on the log!** Even though LeetCode classifies this as "Array," conceptually you're learning the foundational **adjacency matrix representation** — the standard way to encode a graph for matrix-based algorithms. Future graph problems (BFS, DFS, shortest path, connectivity) build on this representation.

---

## Understanding the Goal

For each vertex `i`, count how many edges connect to it. That's the **degree** of vertex `i`.

**Key insight — degree = row sum:**

In an adjacency matrix:
- `matrix[i][j] = 1` means there's an edge between i and j
- `matrix[i][j] = 0` means no edge
- Row `i` has a 1 in each column where vertex `i` has an edge

Therefore, **the degree of vertex `i` = sum of row `i`**.

This is conceptually identical to **LC 1672 (Richest Customer Wealth)** from Day 4B — row sum on a 2D array. The framing is "graph degree" instead of "customer wealth," but the code is the same.

---

## LAYER 1: Line-by-Line Explanation

### Pythonic — sum per row

```python
class Solution:
    def findDegrees(self, matrix: list[list[int]]) -> list[int]:
        # For each row in matrix, sum its elements
        # Row i sums to: count of 1s in row i = degree of vertex i
        # List comp builds the result in one pass
        return [sum(row) for row in matrix]
```

**That's the entire solution.** 1 line. The "row sum = degree" insight collapses everything to a list comprehension.

**Why `sum(row)` works on a list of 0s and 1s:**

`sum([0, 1, 1, 0, 1]) = 3`. Since each entry is 0 or 1, summing literally counts the 1s. No conditional needed.

### With map (functional style)

```python
class Solution:
    def findDegrees(self, matrix):
        # map(func, iterable) applies func to each item
        return list(map(sum, matrix))
```

### Explicit double loop (most verbose)

```python
class Solution:
    def findDegrees(self, matrix):
        n = len(matrix)
        ans = [0] * n                       # Initialize result of size n
        
        # Walk each cell of the matrix
        for i in range(n):
            for j in range(n):
                # Each 1 in row i increments degree of vertex i
                if matrix[i][j] == 1:
                    ans[i] += 1
        return ans
```

Whiteboard-friendly. Shows the underlying mechanics explicitly.

### Column sum (equivalent, since matrix is symmetric)

```python
class Solution:
    def findDegrees(self, matrix):
        n = len(matrix)
        # For undirected graph, matrix[i][j] == matrix[j][i]
        # So row sum == column sum at index i — both give degree
        return [sum(matrix[j][i] for j in range(n)) for i in range(n)]
```

**Works because undirected = symmetric.** For directed graphs they would differ (in-degree vs out-degree).

### NumPy (overkill but slick)

```python
import numpy as np

class Solution:
    def findDegrees(self, matrix):
        return np.sum(matrix, axis=1).tolist()
```

`axis=1` = "collapse columns" (sum across each row). `axis=0` would sum each column.

---

## LAYER 2: Worked Examples

### Example 1: `matrix = [[0,1,1], [1,0,1], [1,1,0]]` → `[2, 2, 2]`

```
       col: 0  1  2
row 0:    [ 0, 1, 1 ]   →  sum = 2 → vertex 0 has degree 2
row 1:    [ 1, 0, 1 ]   →  sum = 2 → vertex 1 has degree 2
row 2:    [ 1, 1, 0 ]   →  sum = 2 → vertex 2 has degree 2
```

Triangle graph — every vertex connects to the other two. Output: `[2, 2, 2]` ✓

### Example 2: `matrix = [[0,1,0], [1,0,0], [0,0,0]]` → `[1, 1, 0]`

```
       col: 0  1  2
row 0:    [ 0, 1, 0 ]   →  sum = 1 → vertex 0 connects to vertex 1
row 1:    [ 1, 0, 0 ]   →  sum = 1 → vertex 1 connects to vertex 0
row 2:    [ 0, 0, 0 ]   →  sum = 0 → vertex 2 is isolated
```

Output: `[1, 1, 0]` ✓

### Example 3: `matrix = [[0]]` → `[0]`

Single isolated vertex. Row 0 is `[0]`, sums to 0. ✓

### Visualizing the symmetry

For `matrix = [[0,1,1], [1,0,1], [1,1,0]]`:

```
              j=0  j=1  j=2
       i=0 [  0    1    1  ]
       i=1 [  1    0    1  ]
       i=2 [  1    1    0  ]
            
Notice: matrix[0][1] = matrix[1][0] = 1   ← same edge, both endpoints
        matrix[0][2] = matrix[2][0] = 1
        matrix[1][2] = matrix[2][1] = 1
        matrix[i][i] = 0 (no self-loops)
```

The matrix is **symmetric across the diagonal.** This is the defining property of an undirected graph in matrix form.

### Edge cases

- **Single vertex (n=1):** matrix is `[[0]]`; row sum is 0
- **Disconnected graph:** vertices with degree 0
- **Complete graph K_n:** every off-diagonal entry is 1; every degree is n-1
- **Tree on n nodes:** degrees sum to 2(n-1) (handshake lemma)
- **Max n=100:** 10,000 cells — instant

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **List comp + sum per row** | **O(n²)** | **O(n)** | **Preferred** |
| Explicit double loop | O(n²) | O(n) | Most readable for whiteboard |
| Map | O(n²) | O(n) | Functional style |
| NumPy | O(n²) | O(n) | Best constant factor for large n |

**Why O(n²) is unavoidable:**

We must look at every cell at least once to know if it's an edge — the matrix itself is n × n = n² entries. **Input size dictates the lower bound.**

If the graph were given as an **adjacency list**, the lower bound would be O(V + E). For sparse graphs, that's much better than O(n²).

**Adjacency Matrix vs Adjacency List — the two graph representations:**

| Representation | Space | "Is edge i-j?" | Iterate neighbors of i |
|----------------|-------|----------------|-------------------------|
| **Adjacency matrix** | O(V²) | **O(1)** | O(V) |
| **Adjacency list** | O(V + E) | O(deg(i)) | **O(deg(i))** |

**When to use each:**

- **Adjacency matrix:** dense graphs (E ≈ V²), need O(1) edge lookup, matrix-based algorithms
- **Adjacency list:** sparse graphs (E << V²), traversal-based algorithms (BFS/DFS)

For this problem you get the matrix; future graph problems on LeetCode usually give you **edges** like `[[0,1], [1,2], ...]`, and you'll need to **build your own adjacency list:**

```python
from collections import defaultdict

graph = defaultdict(list)
for u, v in edges:
    graph[u].append(v)
    graph[v].append(u)        # Both directions for undirected
```

This is the **graph construction template** for nearly every future graph problem.

**The handshake lemma:**

> Sum of all vertex degrees = 2 × (number of edges)

Each edge contributes +1 to two vertices' degrees. So:

```python
total_degree = sum(sum(row) for row in matrix)
num_edges = total_degree // 2
```

Useful sanity check: if your code returns degrees summing to an odd number, you have a bug.

**Connection to LC 1672 (Day 4B, Richest Customer Wealth):**

| LC 1672 | LC 3898 |
|---------|---------|
| accounts[i][j] = balance | matrix[i][j] = edge indicator |
| Max wealth across customers | Degree per vertex |
| `max(sum(row) for row in accounts)` | `[sum(row) for row in matrix]` |

**Same skeleton** — per-row reduction, then aggregate. Only the aggregation differs:
- LC 1672: `max` of row sums
- LC 3898: list of row sums

You've internalized "per-row reduce." New domains (graphs!) reuse it.

**The "degree" concept across graph problems:**

Knowing degrees unlocks:
- **Detect isolated vertices:** degree 0
- **Find leaves (in trees):** degree 1
- **Eulerian path/circuit:** every vertex has even degree
- **Topological sort (Kahn's algorithm):** start from in-degree 0
- **Bipartite check:** uses degrees plus 2-coloring
- **Graph density:** average degree = 2E / V

This Easy problem is teaching you **graph terminology and the matrix representation** — both essential prerequisites for harder graph problems.

**Why undirected → symmetric:**

If "0 and 1 are connected" is a fact about the unordered pair {0, 1}, both `matrix[0][1]` and `matrix[1][0]` must equal 1 — they encode the same fact. For directed graphs (e.g., "0 follows 1 on Twitter"), `matrix[i][j]` and `matrix[j][i]` are independent.

**`matrix[i][i] == 0` constraint:**

The diagonal is always 0 — no **self-loops**. Standard convention, but not universal.

---

## LAYER 4: Interview Variations

• **Directed graph — in-degree vs out-degree:** Out-degree = row sum; in-degree = column sum.
• **Convert matrix to adjacency list:** `{i: [j for j in range(n) if matrix[i][j]] for i in range(n)}`.
• **Number of edges:** `sum(sum(row) for row in matrix) // 2`.
• **Maximum degree:** `max(sum(row) for row in matrix)`.
• **Minimum degree:** `min(sum(row) for row in matrix)`.
• **Isolated vertices:** `[i for i in range(n) if sum(matrix[i]) == 0]`.
• **Leaves (degree 1):** `[i for i in range(n) if sum(matrix[i]) == 1]`.
• **Is graph complete (K_n)?** Every off-diagonal is 1 → every degree is n-1.
• **Is graph a tree?** Connected + exactly n-1 edges.
• **Highest-degree hubs:** `sorted(range(n), key=lambda i: -sum(matrix[i]))[:k]`.
• **Edges list from matrix:** `[(i, j) for i in range(n) for j in range(i+1, n) if matrix[i][j]]`.
• **Weighted graph degree (sum of weights):** Same row sum, entries are weights.
• **Build adjacency matrix from edges:** Initialize zero matrix, set `matrix[u][v] = matrix[v][u] = 1`.
• **Graph properties from degrees:** Eulerian (all even), tree-like (sum to 2(n-1)), regular (all equal).

---

## LAYER 5: Cheat Sheet

```python
# Preferred — one-line list comprehension
class Solution:
    def findDegrees(self, matrix: list[list[int]]) -> list[int]:
        return [sum(row) for row in matrix]
```

**Map-based:**
```python
def findDegrees(self, matrix):
    return list(map(sum, matrix))
```

**Explicit double loop:**
```python
def findDegrees(self, matrix):
    n = len(matrix)
    ans = [0] * n
    for i in range(n):
        for j in range(n):
            if matrix[i][j] == 1:
                ans[i] += 1
    return ans
```

**Column sum:**
```python
def findDegrees(self, matrix):
    n = len(matrix)
    return [sum(matrix[j][i] for j in range(n)) for i in range(n)]
```

**NumPy:**
```python
import numpy as np

def findDegrees(self, matrix):
    return np.sum(matrix, axis=1).tolist()
```

**Building adjacency list at the same time (template for future graph problems):**
```python
def findDegrees(self, matrix):
    n = len(matrix)
    adj = [[] for _ in range(n)]
    degrees = [0] * n
    for i in range(n):
        for j in range(n):
            if matrix[i][j] == 1:
                adj[i].append(j)
                degrees[i] += 1
    return degrees     # adj is bonus data structure for future operations
```

---

**Time:** O(n²) | **Space:** O(n) output (O(1) auxiliary)

**Pattern flag:** "Per-row reduction on a matrix" → **`[reduce(row) for row in matrix]`**. For graphs in adjacency matrix form:
- **Degree** = row sum (undirected) or out-degree row / in-degree column (directed)
- **Number of edges** = `sum(degrees) // 2`
- **Isolated vertices** = vertices with degree 0
- **Symmetric matrix** = undirected graph

Conceptually identical to **LC 1672 (Richest Customer Wealth)** — same per-row sum, different framing. Recognize that **"adjacency matrix" is just a special-purpose 2D array** and matrix techniques transfer directly.

🎯 **First Graph-classified problem on the log.** Future graph problems will switch you to **adjacency list representation** — but you should always be able to convert:

```python
# Matrix → adjacency list:
adj = {i: [j for j in range(n) if matrix[i][j]] for i in range(n)}

# Adjacency list → matrix:
matrix = [[0]*n for _ in range(n)]
for u, neighbors in adj.items():
    for v in neighbors:
        matrix[u][v] = 1
```

Knowing both representations and when to use which is fundamental graph-algorithm literacy.
