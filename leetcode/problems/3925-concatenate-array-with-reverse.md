# 3925. Concatenate Array With Reverse

| | |
|---|---|
| **Difficulty** | Easy |
| **Pattern** | Arrays / strings |
| **Date** | 2026-07-19 |
| **Solve Mode** | `guided` |
| **Time to solve** | ~10 min |
| **Link** | https://leetcode.com/problems/concatenate-array-with-reverse/ |

---

## 1. Input / Output

**Input:**
```
nums = [1, 2, 3]
```

**Output:**
```
[1, 2, 3, 3, 2, 1]
```

**Constraints that matter:**
- `1 <= nums.length <= 100` — tiny; no performance pressure, O(n) is trivially fine.
- The formula `ans[i+n] = nums[n-i-1]` is just "second half = first half reversed."

---

## 2. Data structure used

- Plain Python `list`. No auxiliary structure needed.
- The whole problem is list construction: build a reversed copy, concatenate.

---

## 3. Why this approach works

The second half must be `nums` reversed **without disturbing the first half**. So I use
operations that build *new* lists — `+` and `[::-1]` — rather than ones that reverse in
place (`nums.reverse()`), which would corrupt the first half. Because both the slice and the
`+` produce new lists, `nums` is never mutated and the two halves stay independent.

---

## 4. Time / space complexity

| | |
|---|---|
| **Time** | O(n) — one pass to reverse, one to concatenate |
| **Space** | O(n) — output is size 2n, unavoidable since it must be returned |
| **Naive approach** | Same O(n); no worse alternative to avoid here |

---

## My solution

```python
class Solution:
    def concatWithReverse(self, nums: list[int]) -> list[int]:
        return nums + nums[::-1]
```

Alternative (more explicit, most readable intent):
```python
class Solution:
    def concatWithReverse(self, nums: list[int]) -> list[int]:
        res = nums.copy()
        res.extend(nums[::-1])
        return res
```

---

## What went wrong first

Nothing broke — all three attempts were correct. The lesson wasn't a bug, it was the
**mutation distinction**: `reversed()` returns a lazy iterator (no new list, no mutation);
`nums[::-1]` returns a new list; `nums.reverse()` mutates in place and returns `None`;
`a + b` builds new, `a.extend(b)` mutates `a`. All three of my solutions happened to pick
non-mutating tools — the trap would've been `nums.reverse()` used as the first half.

| Op | New list? | Mutates original? | Returns |
|---|---|---|---|
| `reversed(nums)` | no (iterator) | no | iterator |
| `nums[::-1]` | yes | no | new list |
| `nums.reverse()` | no | **yes** | `None` |
| `a + b` | yes | no | new list |
| `a.extend(b)` | no | **yes** | `None` |

---

## Cold variant rep

| | |
|---|---|
| **Variant problem** | Interleave-with-mirror: `ans[2i]=nums[i]`, `ans[2i+1]=nums[n-1-i]` |
| **Date** | — |
| **Solved unaided?** | **OPEN — not yet attempted** |
| **Notes** | Forces the `n-1-i` index arithmetic the slice solutions let me skip. Required before this counts as closed. |

---

## Interview sentence

> On array construction I default to slicing and concatenation because both build new lists —
> the bug people hit is reaching for an in-place reverse and corrupting the half they meant
> to keep.
