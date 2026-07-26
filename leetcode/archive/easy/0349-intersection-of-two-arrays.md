# 349. Intersection of Two Arrays

**Difficulty:** Easy
**Pattern:** Two Pointers / Hash Set
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

Return the **distinct** values that appear in both `nums1` and `nums2`. Order doesn't matter, duplicates in the input are ignored — `[2,2,2]` ∩ `[2,2]` = `[2]`.

**Key insight:** The "unique result" constraint screams **set**. Three reasonable approaches:
1. **Two sets**: `set(nums1) & set(nums2)` — one line, O(n+m), the natural Pythonic answer
2. **Sort + two pointers** (the editorial's approach): O(n log n + m log m), good when memory matters
3. **One set + lookup**: O(n+m), build set from smaller array, scan the other

All three are valid. The editorial chose two pointers to teach the technique, but in a real interview you'd reach for set intersection first and mention two pointers as the no-extra-memory alternative.

---

## LAYER 1: Line-by-Line Explanation

**Direct Python3 port of the Java editorial (Two Pointers):**

```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        # Sort both arrays
        nums1.sort()
        nums2.sort()

        # Initialize two pointers
        n, m = len(nums1), len(nums2)
        p1, p2 = 0, 0

        # Store intersection values (set guarantees uniqueness)
        intersection = set()

        # Walk both arrays in tandem
        while p1 < n and p2 < m:
            if nums1[p1] == nums2[p2]:
                intersection.add(nums1[p1])
                p1 += 1
                p2 += 1
            elif nums1[p1] < nums2[p2]:
                p1 += 1                     # nums1's value is too small, advance it
            else:
                p2 += 1                     # nums2's value is too small, advance it

        return list(intersection)
```

**Why two pointers works on sorted arrays:** When the two pointed values are equal, we've found a match — add it and advance both. When they differ, the smaller one CAN'T match anything ahead in the other array (since the other array is sorted and we're past where that small value would appear), so we advance the smaller pointer. The set absorbs duplicates automatically.

**Pythonic version (recommended in interviews):**

```python
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        return list(set(nums1) & set(nums2))
```

**What `&` does on sets:** It's the set-intersection operator — returns a new set containing only elements present in both. Python implements it as: build the smaller set into a hash table, then iterate the larger one and keep elements that hash-hit. O(n + m).

---

## LAYER 2: Worked Examples

**Example 1: `nums1 = [1,2,2,1]`, `nums2 = [2,2]` → `[2]`**

*Set approach:*
- `set(nums1) = {1, 2}`
- `set(nums2) = {2}`
- `{1, 2} & {2} = {2}` → `[2]` ✓

*Two-pointer approach (after sorting):*

`nums1 = [1,1,2,2]`, `nums2 = [2,2]`

| step | p1 | p2 | nums1[p1] | nums2[p2] | action |
|------|----|----|-----------|-----------|--------|
| 1 | 0 | 0 | 1 | 2 | 1 < 2 → p1++ |
| 2 | 1 | 0 | 1 | 2 | 1 < 2 → p1++ |
| 3 | 2 | 0 | 2 | 2 | equal → add 2, p1++, p2++ |
| 4 | 3 | 1 | 2 | 2 | equal → add 2 (no-op, already in set), p1++, p2++ |
| 5 | 4 | 2 | — | — | loop ends |

`intersection = {2}` → `[2]` ✓

**Example 2: `nums1 = [4,9,5]`, `nums2 = [9,4,9,8,4]` → `[9,4]` (or `[4,9]`)**

*Set approach:*
- `set(nums1) = {4, 5, 9}`
- `set(nums2) = {4, 8, 9}`
- `{4, 5, 9} & {4, 8, 9} = {4, 9}` → `[4, 9]` or `[9, 4]` ✓

*Two-pointer approach:*

After sort: `nums1 = [4,5,9]`, `nums2 = [4,4,8,9,9]`

| step | p1 | p2 | nums1[p1] | nums2[p2] | action |
|------|----|----|-----------|-----------|--------|
| 1 | 0 | 0 | 4 | 4 | equal → add 4, p1++, p2++ |
| 2 | 1 | 1 | 5 | 4 | 5 > 4 → p2++ |
| 3 | 1 | 2 | 5 | 8 | 5 < 8 → p1++ |
| 4 | 2 | 2 | 9 | 8 | 9 > 8 → p2++ |
| 5 | 2 | 3 | 9 | 9 | equal → add 9, p1++, p2++ |
| 6 | 3 | 4 | — | — | loop ends |

`intersection = {4, 9}` → `[4, 9]` ✓

**Edge cases:**
- No overlap: `[1,2,3]` ∩ `[4,5,6]` → `[]`
- One is subset: `[1]` ∩ `[1,2,3]` → `[1]`
- Identical: `[1,2,3]` ∩ `[1,2,3]` → `[1,2,3]`
- All duplicates: `[7,7,7]` ∩ `[7,7]` → `[7]`
- Empty (not in constraints, but cleanly handled): one empty → `[]`

---

## LAYER 3: Key Insights

| Approach | Time | Space | When to use |
|----------|------|-------|-------------|
| Two sets (`&`) | O(n + m) | O(n + m) | **Default — cleanest** |
| Sort + two pointers | O(n log n + m log m) | O(min(n, m)) for set, O(1) if outputting in place | When inputs are already sorted, or memory tight |
| One set + scan | O(n + m) | O(min(n, m)) | When one array is much smaller |
| Brute force (nested loop + uniqueness check) | O(n × m) | O(min(n,m)) | Never (only for tiny inputs) |
| Binary search per element | O((n+m) log min) | O(min) | Niche: streaming queries |

**Why two pointers is taught even when sets are faster:** It teaches a general technique that generalizes to:
- Merging sorted arrays (LC 88)
- Finding intersection of K arrays (extends naturally)
- Streaming intersection where you can't load both arrays into memory
- Database join algorithms (sort-merge join uses this exact pattern)

**Why the editorial uses sort + pointers instead of `set & set`:**
- It avoids hash collisions in adversarial inputs
- It uses O(1) extra memory if you output directly (no set needed)
- It's the more "general" technique that ports to other languages without hash sets
- It's a teaching opportunity — sets feel like magic; sort + pointers shows you the algorithm

**Subtle point — when input is ALREADY sorted:**
The two-pointer approach drops to O(n + m), beating the set approach (since sets have hash overhead). LeetCode 350 (Intersection of Two Arrays II) follows up on this idea.

**Subtle point — duplicates in input:**
The constraint says "Each element in the result must be unique" — but inputs CAN have duplicates. The set absorbs these automatically. In the two-pointer code, when we match, both pointers advance; the next iteration might find the same value again (if duplicates exist on both sides), but `set.add(x)` is idempotent so it doesn't matter.

---

## LAYER 4: Interview Variations

• **Return elements with their min frequency** (LC 350 Intersection of Two Arrays II):
  Different problem. `[1,2,2,1]` ∩ `[2,2]` becomes `[2,2]` (frequency-aware).
  Use `Counter`: `(Counter(nums1) & Counter(nums2)).elements()`.

• **Intersection of K arrays:**
  `set.intersection(*[set(a) for a in arrays])` — varargs unpacking.

• **Intersection of two sorted arrays without extra space:**
  Two pointers with output written to the smaller array in place. O(n + m) time, O(1) extra space.

• **Intersection of two streams (can't fit in memory):**
  Sort both via external merge sort, then run two-pointer. Classic database technique.

• **Symmetric difference** (in one but not both):
  `set(nums1) ^ set(nums2)` — XOR on sets.

• **Union** (in either):
  `set(nums1) | set(nums2)`.

• **Difference** (in nums1 but not nums2):
  `set(nums1) - set(nums2)`.

• **Sorted intersection output:**
  `sorted(set(nums1) & set(nums2))`.

• **Intersection with frequency-weighted output:**
  See LC 350. Use `Counter` instead of `set`.

• **Approximate intersection on huge data:**
  Use a Bloom filter — build BF from smaller array, scan larger, false positives possible.

• **Intersection by some property (e.g., same hash, same prefix):**
  Group by property in a dict, then intersect keys.

• **Online: nums1 fixed, nums2 arrives as queries:**
  Preprocess nums1 into a set once (O(n)). Each query is O(1).

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Pythonic, O(n+m)
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        return list(set(nums1) & set(nums2))
```

**Editorial port — Two pointers, O(n log n + m log m):**
```python
def intersection(nums1, nums2):
    nums1.sort()
    nums2.sort()
    p1, p2 = 0, 0
    result = set()
    while p1 < len(nums1) and p2 < len(nums2):
        if nums1[p1] == nums2[p2]:
            result.add(nums1[p1])
            p1 += 1
            p2 += 1
        elif nums1[p1] < nums2[p2]:
            p1 += 1
        else:
            p2 += 1
    return list(result)
```

**Hash set lookup (build small, scan large):**
```python
def intersection(nums1, nums2):
    if len(nums1) > len(nums2):
        nums1, nums2 = nums2, nums1
    small = set(nums1)
    return list({x for x in nums2 if x in small})
```

**One-liner with intersection method:**
```python
def intersection(nums1, nums2):
    return list(set(nums1).intersection(nums2))
```

---

**Time:** O(n + m) | **Space:** O(n + m)

**Pattern flag:** Two sorted sequences + need to find matches → **two pointers**. Need uniqueness in output → **set**. Need frequency-aware output → **Counter**. These three primitives cover the majority of intersection-style problems.
