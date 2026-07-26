# 360. Sort Transformed Array

| | |
|---|---|
| **Difficulty** | Medium |
| **Family** | Two Pointers |
| **Date** | 2026-07-24 |
| **Mode** | First-Pass Assisted (pattern explained, then written myself) |
| **Link** | https://leetcode.com/problems/sort-transformed-array/ |

---

## 1. Input / Output
```
input  → nums = [-4,-2,2,4], a = 1, b = 3, c = 5
output → [3,9,15,33]
```
Constraints that matter:
- `nums` is already sorted ascending — this is what makes two pointers possible.
- `a` can be 0 → the quadratic degenerates to a line; must still work.

## 2. Data structure used
- Output list + two pointers (`left`, `right`) into the sorted input.

## 3. Why this approach works
`f(x) = ax² + bx + c` is a **parabola**, so over a sorted array its extreme values sit at the
**ends**, not the middle. If `a > 0` the parabola opens up → the two ends hold the largest
values; if `a < 0` it opens down → the ends hold the smallest. So I converge two pointers from
both ends, comparing transformed endpoint values and taking the extreme each step. `a = 0` is
linear (monotonic), so its max is still at an end — the same machinery covers it.

## 4. Time / space complexity
- Time: O(n) — single two-pointer pass, `transform` is O(1). (Answers the follow-up.)
- Space: O(n) — the output.

---

## My solution
```python
class Solution:
    def sortTransformedArray(self, nums, a, b, c):
        def transform(x):
            return a * x * x + b * x + c
        answer = []
        left, right = 0, len(nums) - 1
        if a < 0:                      # opens down: extremes are minima → fill low end first
            while left <= right:
                lv, rv = transform(nums[left]), transform(nums[right])
                if lv < rv: answer.append(lv); left += 1
                else:       answer.append(rv); right -= 1
        else:                          # opens up (or linear): extremes are maxima → fill then reverse
            while left <= right:
                lv, rv = transform(nums[left]), transform(nums[right])
                if lv > rv: answer.append(lv); left += 1
                else:       answer.append(rv); right -= 1
            answer.reverse()
        return answer
```

## What tripped me up
Nothing broke — verified against both examples and the `a = 0` linear case. The real note is a
**refactor**, not a bug: the two branches are near-identical mirror images. The clean version
pre-allocates `answer = [0]*len(nums)`, keeps one loop, and writes each picked value to the
correct end (from the back when `a >= 0`, from the front when `a < 0`) — no `.reverse()`, no
duplicated logic. That unification is the next-day recall rep.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | **Owed** — rebuild as the single-loop unified version |
| **Where I got stuck** | — |

## Interview sentence
> Because the transform is a parabola, the extreme values of a sorted array land at its ends,
> so I sort in O(n) with two pointers converging inward — the sign of `a` just decides whether
> the ends are the maxima or the minima.
