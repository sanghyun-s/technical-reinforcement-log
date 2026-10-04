# 88. Merge Sorted Array

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | Two Pointers (merge) |
| **Date** | 2026-08-11 |
| **Mode** | Reference Recall (A1 hand-rewritten, A2 from Google) |
| **Link** | https://leetcode.com/problems/merge-sorted-array/ |

---

## 1. Input / Output
```
input  → nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
output → nums1 = [1,2,2,3,5,6]   (in place, no return)
```
Merge sorted `nums2` into `nums1` in place; the last `n` slots of `nums1` are zero-padding.

## 2. Data structure used
- Three index pointers into `nums1`/`nums2`. No extra array.

## 3. Why this approach works
Both arrays are already sorted — so don't re-sort, **merge**. The trick is to fill **from the
back**: pointers `p1` (end of nums1's real values), `p2` (end of nums2), `p_write` (end of
nums1). Write the larger of the two into `p_write` and move inward. Backwards because the free
space is at the **tail** — filling front-to-back would overwrite nums1 values before reading
them. **Merge in place + free space at the end ⇒ walk backwards.**

## 4. Time / space complexity
- Approach 2 (back-merge): **O(m+n)** time, O(1) extra. (Answers the follow-up.)
- Approach 1 (concat + sort): O((m+n)·log(m+n)) — throws away the given sorted-ness.

---

## My solutions
```python
# 1 — concat + sort: simple, pragmatic at n<=200, but ignores the sorted inputs
for i in range(n): nums1[i+m] = nums2[i]
nums1.sort()

# 2 — merge from the back, O(m+n) (the intended answer)
p1, p2, w = m-1, n-1, m+n-1
while p1 >= 0 and p2 >= 0:
    if nums1[p1] > nums2[p2]: nums1[w] = nums1[p1]; p1 -= 1
    else:                     nums1[w] = nums2[p2]; p2 -= 1
    w -= 1
while p2 >= 0:               # drain nums2 leftovers only
    nums1[w] = nums2[p2]; p2 -= 1; w -= 1
```

## What tripped me up
Nothing broke. Two things I got right worth noticing: (1) the leftover loop drains **only p2** —
nums1's remaining values are already in their final slots, so there's nothing to move; (2) edge
cases m=0 and n=0 fall out for free (the loops skip appropriately).

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | Owed |
| **Where I got stuck** | — |

## Interview sentence
> Both inputs are sorted and I merge in place, so I go from the back with three pointers —
> O(m+n) — writing the larger value into the tail and moving inward. Backwards specifically
> because the free space is at the end, so I never overwrite a value I haven't read yet.
