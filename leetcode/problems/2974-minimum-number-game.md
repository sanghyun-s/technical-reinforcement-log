# 2974. Minimum Number Game

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | Sorting |
| **Date** | 2026-07-26 |
| **Mode** | Reference Recall (researched both approaches, hand-typed) |
| **Link** | https://leetcode.com/problems/minimum-number-game/ |

---

## 1. Input / Output
```
input  → nums = [5,4,2,3]
output → [3,2,5,4]
```
Constraints that matter:
- `nums.length` is even → pair processing never runs off the end.
- `1 <= nums[i] <= 100` → values are small & bounded (relevant to Approach 2 below).

## 2. Data structure used
- Approach 1: in-place sort + index stepping. Approach 2: a counts/bucket array.

## 3. Why this approach works
Each round, Alice takes the min and Bob the next min, then Bob appends before Alice — so within
each sorted pair the **larger** element lands first. Sort ascending, then swap each adjacent
pair. `range(0, len, 2)` lands on each pair's front; the tuple-swap flips them in place.

## 4. Time / space complexity
- Approach 1: O(n log n) time (sort dominates), O(1) extra.
- Approach 2: O(n + k) time (k = max value), O(k) space — technically O(n) when k is bounded.

---

## My solution
```python
# Approach 1 — the one to submit
class Solution:
    def numberGame(self, nums: List[int]) -> List[int]:
        nums.sort()
        for i in range(0, len(nums), 2):
            nums[i], nums[i + 1] = nums[i + 1], nums[i]
        return nums
```

## What tripped me up
Both approaches were correct. The lesson is **judgment, not correctness**: I also wrote a
counting-sort version (Approach 2), which is faster on paper (O(n+k)) — but it's the wrong
instinct here.
1. ~15 lines to do what 4 lines do; in an interview that reads as not seeing the simple path.
2. It only works because values are bounded 1–100. Allow large/negative values and it breaks
   while the plain sort sails through. Tying the solution to a narrow constraint is fragile.
3. The `alice_pick` toggle is hand-managed state — a small bug surface the 4-liner avoids.

The counting-sort version stays in my notes as the *alternative*, not the answer.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | **Owed** |
| **Where I got stuck** | — |

## Interview sentence
> I could do it in O(n) with counting sort, but the values would have to stay bounded — for
> this input a plain sort with an adjacent-pair swap is clearer and robust, and I'd only reach
> for counting sort if profiling said the sort was the bottleneck.
