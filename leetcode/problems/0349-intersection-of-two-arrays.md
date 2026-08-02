# 349. Intersection of Two Arrays

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | Hash / Set |
| **Date** | 2026-07-30 |
| **Mode** | Reference Recall (re-drill of archived 349, hand-typed) |
| **Link** | https://leetcode.com/problems/intersection-of-two-arrays/ |

---

## 1. Input / Output
```
input  → nums1 = [1,2,2,1], nums2 = [2,2]
output → [2]
```
Return the **unique** values present in both, any order.

## 2. Data structure used
- Sets. "Unique elements in both" is the literal definition of set intersection.

## 3. Why this approach works
A set removes duplicates and gives O(1) membership. Intersecting two sets is O(n+m) and returns
exactly "values in both, deduplicated" — which is the problem verbatim. No traversal logic needed.

## 4. Time / space complexity
- Set intersection: O(n + m).
- Sorted two-pointer (Approach 1): O(n log n) — worse here; only wins if inputs are pre-sorted.

---

## My solutions (4, cheapest to most elaborate)
```python
# 2 — the answer: say what the problem asks
return list(set(nums1) & set(nums2))

# 4 — same, method form; .intersection() takes the raw list so you skip building the 2nd set
return list(set(nums1).intersection(nums2))

# 3 — "hash the smaller side": keep the set (memory) as small as possible, iterate the larger
if len(nums1) > len(nums2): nums1, nums2 = nums2, nums1
small = set(nums1)
return list({x for x in nums2 if x in small})

# 1 — sorted two-pointer: correct but O(n log n); the right tool only if inputs are pre-sorted
```

## What tripped me up
Nothing broke. The lesson is tool selection: I reached for the sorted two-pointer (Approach 1),
but for *this* problem the one-line set intersection is both faster (O(n+m) vs O(n log n)) and
clearer. Recognizing the fancy version isn't needed is the recurring judgment — same as 2974,
2824, 1874. Approach 3's "hash the smaller side" is the one genuinely worth keeping for the
*reasoning*: it's a real optimization when input sizes differ a lot.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | **Owed** |
| **Where I got stuck** | — |

## Interview sentence
> Intersection of two arrays is set intersection — `set(a) & set(b)` — because "unique values in
> both" is the definition of a set intersect, and it's O(n+m). I'd only use the sorted
> two-pointer if the inputs were already sorted or I couldn't spare the hash memory.
