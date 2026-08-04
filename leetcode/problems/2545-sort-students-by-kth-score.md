# 2545. Sort the Students by Their Kth Score

| | |
|---|---|
| **Difficulty** | Medium |
| **Family** | Sorting (key function) |
| **Date** | 2026-07-31 |
| **Mode** | Mixed — Approach 1 recall, rest Google-researched (not in archive) |
| **Link** | https://leetcode.com/problems/sort-the-students-by-their-kth-score/ |

---

## 1. Input / Output
```
input  → score = [[10,6,9,1],[7,5,11,2],[4,8,3,15]], k = 2
output → [[7,5,11,2],[10,6,9,1],[4,8,3,15]]
```
Sort the rows by column `k`, highest first.

## 2. Data structure used
- The matrix itself + Python's sort with a `key` function.

## 3. Why this approach works
The whole task is "sort rows by one column, descending." `sorted(rows, key=..., reverse=True)`
is exactly that. `key` picks the ranking value per row (`row[k]`); `reverse=True` makes it
high→low. Distinct integers → no tie-breaking to worry about.

## 4. Time / space complexity
- O(m log m) comparisons, each O(1) (single int lookup). Row width n doesn't affect comparison cost.

---

## My solutions
```python
# 1 — sorted() returns a NEW list, leaves input untouched (SUBMIT)
return sorted(score, key=lambda row: row[k], reverse=True)

# 2 — .sort() mutates in place, returns None (hence `return score` on its own line)
score.sort(key=lambda row: row[k], reverse=True); return score

# 3 — itemgetter(k): C-implemented, clearer intent, a touch faster than the lambda
from operator import itemgetter
return sorted(score, key=itemgetter(k), reverse=True)
```

## What tripped me up
Nothing. The real content is the **mutate vs. return** distinction: `sorted()` builds a new list
(safe if the caller still needs the original); `.sort()` mutates in place and returns `None`
(saves memory). Same distinction that's run through the whole log — the reverse-in-place trap,
`give_raise` vs `monthly_salary`, BST accumulators. `itemgetter` over a lambda is the idiomatic
"I know the stdlib" tell.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | Owed (trivial — the value is the idioms, not the algorithm) |
| **Where I got stuck** | — |

## Interview sentence
> It's a sort by a single column key — `sorted(rows, key=itemgetter(k), reverse=True)`. I'd use
> `sorted` for a new list or `.sort()` to mutate in place, and `itemgetter` over a lambda because
> it's clearer and a touch faster.
