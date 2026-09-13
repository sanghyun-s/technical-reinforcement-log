# 3898. Find the Degree of Each Vertex

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | Graph / Matrix |
| **Date** | 2026-09-14 |
| **Mode** | Reference Recall (A1 rewritten/submitted, A2–A5 hand-typed from archive) |
| **Link** | https://leetcode.com/problems/find-the-degree-of-each-vertex/ |

---

## 1. Input / Output
```
input  → matrix = [[0,1,1],[1,0,1],[1,1,0]]
output → [2,2,2]
```
Given an adjacency matrix, return each vertex's degree (edge count).

## 2. The insight — degree of vertex i = sum of row i
`matrix[i][j] = 1` means an edge between i and j, so the number of edges touching i is the count
of 1s in **row i** = `sum(row i)` (entries are 0/1). The graph framing dissolves into "sum each
row of a 2D array" — no traversal needed. Recognising an adjacency-matrix degree is a row-sum is
the transferable move.

## 3. Complexity
**All five are O(n²)** — the input is n² cells and you must read each to count edges. The short
one-liners still walk every element; there is no sub-O(n²) solution because the input is O(n²).
Differences between approaches are style + constant factors (NumPy's C loop), never asymptotic.

---

## My solutions
```python
# 1 — idiomatic submit
return [sum(row) for row in matrix]

# 2 — functional: map(sum, matrix)
# 3 — explicit double loop (language-agnostic; increment ans[i] per 1)
# 4 — column sum: sum(matrix[j][i] for j in range(n)) — valid because the graph is UNDIRECTED,
#     so matrix is symmetric (matrix[i][j]==matrix[j][i]); row i == column i.
# 5 — NumPy: np.sum(matrix, axis=1).tolist()  (axis=1 = sum along rows; C-speed on large input)
```

## What tripped me up
Nothing. The standout is A4's **symmetry catch**: I read the constraint `matrix[i][j]==matrix[j][i]`
(undirected ⇒ symmetric matrix), so column-sum equals row-sum — never *preferred*, but proving I
understood the structure, not just the recipe. NumPy caveat: fine on LeetCode, but in an interview
present it as "for real numerical work at scale," not as the primary answer to a plain-list problem.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | Owed |
| **Where I got stuck** | — |

## Interview sentence
> Degree of vertex i is the count of 1s in row i, so it's `[sum(row) for row in matrix]` — O(n²)
> because the matrix is O(n²) and you must read every cell. The graph is undirected so the matrix
> is symmetric; summing column i works identically.
