# 1637. Widest Vertical Area Between Two Points Containing No Points

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | Sorting |
| **Date** | 2026-08-16 |
| **Mode** | Reference Recall (A1 rewritten/submitted, A2–4 hand-typed from archive) |
| **Link** | https://leetcode.com/problems/widest-vertical-area-between-two-points-containing-no-points/ |

---

## 1. Input / Output
```
input  → points = [[3,1],[9,0],[1,0],[1,4],[5,3],[8,8]]
output → 3
```
Widest infinitely-tall vertical strip containing no point (edges excluded).

## 2. The insight — y is a red herring
A vertical strip is **infinitely tall**, so a point's height never matters — only its
x-position decides if it's inside. The 2D framing is a disguise: it collapses to a **1D**
problem — sort the x-values, find the biggest gap between adjacent ones. (Hint 2 nudges you to
discard half the input; same "read what the problem *is*" move as 3945.)

## 3. Complexity
O(n log n), sort-dominated; the gap scan is O(n). The sort is unavoidable — you must order the
x's to know which are adjacent.

---

## My solutions (4 dialects of one idea)
```python
# 2 — clean idiom (submit): adjacent-pair scan
xs = sorted(p[0] for p in points)
return max(b - a for a, b in zip(xs, xs[1:]))     # zip(xs, xs[1:]) = neighbors

# 4 — pairwise (Py 3.10+): the purpose-built stdlib version, most self-documenting
from itertools import pairwise
return max(b - a for a, b in pairwise(xs))

# 3 — points.sort() then index (tuples sort by x first) — but MUTATES points
# 1 — explicit index loop over xs
```

## Notes
- **Mutate vs return:** A1/2/4 build a new sorted `xs` (leave `points` intact); A3 `.sort()`
  mutates the caller's list. Recurring distinction.
- `pairwise` over `zip(xs, xs[1:])` is the "I know the stdlib" signal (cf. `itemgetter` in 2545).

## Interview sentence
> A vertical strip is infinitely tall, so y never matters — it's a 1D problem. Sort the
> x-coordinates and return the max gap between adjacent ones, O(n log n). I'd write the scan with
> `pairwise`.
