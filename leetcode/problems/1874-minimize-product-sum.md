# 1874. Minimize Product Sum of Two Arrays

| | |
|---|---|
| **Difficulty** | Medium |
| **Family** | Greedy (+ counting sort) |
| **Date** | 2026-07-26 |
| **Mode** | Reference Recall (re-drill of archived greedy set) |
| **Link** | https://leetcode.com/problems/minimize-product-sum-of-two-arrays/ |

---

## 1. Input / Output
```
input  → nums1 = [5,3,4,2], nums2 = [4,2,2,5]
output → 40
```
Rearrange nums1 freely to minimize Σ nums1[i]·nums2[i].
Constraints that matter: `1 <= nums[i] <= 100` → bounded values enable counting sort.

## 2. Data structure used
- Approach 1: two sorted arrays. Approach 2: two count/bucket arrays (size 101).

## 3. Why this approach works
**Rearrangement inequality.** To *minimize* a sum of paired products, pair the **smallest of one
array with the largest of the other**. Sort nums1 ascending, nums2 descending, zip, multiply,
sum. (Small-with-large minimizes; small-with-small maximizes — the named theorem is the proof.)

## 4. Time / space complexity
- Approach 1 (sort): O(n log n), O(1) extra.
- Approach 2 (counting sort): O(n + k), k = 100 → effectively O(n).

---

## My solution
```python
# Approach 1 — sort ascending × descending, the clean answer
class Solution:
    def minProductSum(self, nums1, nums2):
        nums1.sort()
        nums2.sort(reverse=True)          # descending (comment on this line was wrong — fixed)
        return sum(a * b for a, b in zip(nums1, nums2))
```
Approach 2 (counting sort — faster, but only because values are bounded):
```python
        count1, count2 = [0]*101, [0]*101
        for x in nums1: count1[x] += 1
        for x in nums2: count2[x] += 1
        ans, p1, p2 = 0, 1, 100
        while p1 <= 100 and p2 >= 1:
            if   count1[p1] == 0: p1 += 1
            elif count2[p2] == 0: p2 -= 1
            else:
                pairs = min(count1[p1], count2[p2])
                ans += pairs * p1 * p2
                count1[p1] -= pairs; count2[p2] -= pairs
        # return ans
```

## What tripped me up
Nothing broke. Two notes: (1) Approach 1's inline comment said "ascending" while the code sorts
descending — a comment that contradicts the code, now fixed. (2) The counting-sort version only
works because values are ≤ 100 — the **third** problem today (with 2974, 2824) where the same
judgment recurs: *counting sort is available and faster when values are bounded, but fragile if
they aren't.* Recognizing that reusable decision across problems is the point of the re-drill.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | **Owed** |
| **Where I got stuck** | — |

## Interview sentence
> By the rearrangement inequality I pair ascending with descending to minimize the product sum —
> O(n log n) and robust to any values. If values are bounded and profiling flagged the sort, I'd
> switch to counting sort for O(n) — the same bounded-values trade-off I keep meeting.
