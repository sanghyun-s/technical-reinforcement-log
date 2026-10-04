# 1365. How Many Numbers Are Smaller Than the Current Number

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | Sorting / Counting sort + prefix sum |
| **Date** | 2026-08-11 |
| **Mode** | Reference Recall |
| **Link** | https://leetcode.com/problems/how-many-numbers-are-smaller-than-the-current-number/ |

---

## 1. Input / Output
```
input  → nums = [8,1,2,2,3]
output → [4,0,1,1,3]
```
For each element, count how many others are strictly smaller.

## 2. Data structure used
- Sorted copy + binary search, OR a counting array + prefix sum.

## 3. Why this approach works
A **three-tier ladder**, and the constraint `0 <= nums[i] <= 100` picks the winner:
- **Brute force O(n²)** — nested compare.
- **Sort + `bisect_left` O(n log n)** — in the sorted copy, the index of x's *first* occurrence
  equals "how many are strictly less than x" (everything before it is smaller). Dupes handled free.
- **Counting sort + prefix sum O(n)** — values are bounded, so tally `count[0..100]`, prefix-sum
  to "how many ≤ v," then "strictly less than x" = `count[x-1]`. No log factor. **This is the
  intended optimal** — the *correct* use of the bounded-values trick (unlike 3751 where it was
  overkill).

## 4. Time / space complexity
- Brute O(n²) · sort+bisect O(n log n) · counting sort **O(n + k)**, k = 100.

---

## My solutions
```python
# 2 — sort + binary search: bisect_left(sorted, x) = count strictly less than x
sorted_nums = sorted(nums)
return [bisect_left(sorted_nums, x) for x in nums]

# 3 — counting sort + in-place prefix sum: count[x] = "# <= x", so answer is count[x-1]
count = [0]*101
for x in nums: count[x] += 1
for v in range(1,101): count[v] += count[v-1]
return [count[x-1] if x > 0 else 0 for x in nums]   # x=0 guard is essential

# 4 — separate less_than array: less_than[x] defined directly, no x-1 edge case
```

## What tripped me up
Nothing broke. Two real notes: (1) the **`x=0` guard** in Approach 3 is essential — `count[-1]`
would silently wrap to the last element and return garbage (a nasty no-crash bug); Approach 4
sidesteps it structurally by defining a separate `less_than` array. (2) A3 vs A4 is the
mutate-in-place vs separate-array trade — A3 saves memory, A4 is guard-free and clearer.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | Owed |
| **Where I got stuck** | — |

## Interview sentence
> Brute force is O(n²); sort + `bisect_left` is O(n log n). But values are bounded 0–100, so
> counting sort with a prefix sum gives O(n) — tally, prefix-sum to "how many ≤ v," read off
> "strictly less than x." I'd use a separate less-than array to avoid the x−1 edge case.
