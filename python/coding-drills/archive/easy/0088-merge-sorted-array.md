# 0088. Merge Sorted Array

**Difficulty:** Easy  
**Pattern:** Two Pointers / Sorting  
**Date Solved:** 2026-05-15  
**Acceptance Rate:** 54.8%  
**Status:** ✅

---

## Understanding the Goal

Merge two sorted arrays into one, modify first array in-place.

**Key insight:** Copy nums2 into empty slots of nums1, then sort. Or use two pointers from right for O(m+n).

**Examples Visualized:**

```
nums1 = [1,2,3,0,0,0], m=3
nums2 = [2,5,6], n=3

Step 1: Copy nums2 into slots
[1,2,3,2,5,6]

Step 2: Sort
[1,2,2,3,5,6] ✓
```

---

## LAYER 1: Line-by-Line Explanation

```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        # Copy all elements from nums2 into empty slots of nums1
        # nums1 has m valid elements + n empty slots
        for i in range(n):
            # Place nums2[i] at position m+i in nums1
            nums1[i + m] = nums2[i]
        
        # Sort entire nums1 array in-place
        # Now contains all elements from both arrays
        nums1.sort()
```

---

## LAYER 2: Worked Examples

**Example 1: [1,2,3,0,0,0], m=3, [2,5,6], n=3**
```
Step 1 (Copy):
  i=0: nums1[3+0] = nums2[0] = 2 → [1,2,3,2,0,0]
  i=1: nums1[3+1] = nums2[1] = 5 → [1,2,3,2,5,0]
  i=2: nums1[3+2] = nums2[2] = 6 → [1,2,3,2,5,6]

Step 2 (Sort):
  nums1.sort() → [1,2,2,3,5,6] ✓
```

**Example 2: [1], m=1, [], n=0**
```
Step 1 (Copy):
  Loop range(0) - no iterations

Step 2 (Sort):
  Already sorted → [1] ✓
```

**Example 3: [0], m=0, [1], n=1**
```
Step 1 (Copy):
  i=0: nums1[0+0] = nums2[0] = 1 → [1]

Step 2 (Sort):
  Already sorted → [1] ✓
```

---

## LAYER 3: Key Insights

**Pattern:** Copy + Sort (Simple but not optimal)

| Aspect | Value |
|--------|-------|
| **Time** | O((m+n) log(m+n)) |
| **Space** | O(1) excluding output |
| **Approach** | Copy then sort |
| **Drawback** | Doesn't achieve O(m+n) time |

**Common mistakes:**
- ❌ Not copying nums2 first (overwrites nums1)
- ❌ Forgetting m and n parameters
- ❌ Modifying array while iterating

**When to use:**
- Simple implementation needed
- O(m+n log(m+n)) acceptable
- Space constraint not critical

---

## LAYER 4: Interview Variations

• **If question asks "O(m+n) time"** → Use two pointers from right-to-left: compare last elements, place larger at end, move backwards until done

• **If question says "don't modify nums1"** → Create new array, fill with merged sorted elements, return new array

• **If question adds "merge three arrays"** → Merge first two, then merge result with third

• **If question asks "in-place without extra space O(1)"** → Two-pointer from both ends with swapping (harder approach)

---

## LAYER 5: Cheat Sheet Entry

**Pattern:** In-Place Array Merge

**Template (Simple O((m+n)log(m+n))):**
```python
def merge(nums1, m, nums2, n):
    for i in range(n):
        nums1[m + i] = nums2[i]
    nums1.sort()
```

**Template (Optimal O(m+n)):**
```python
def merge_optimal(nums1, m, nums2, n):
    p1, p2, p = m-1, n-1, m+n-1
    while p1 >= 0 and p2 >= 0:
        if nums1[p1] > nums2[p2]:
            nums1[p] = nums1[p1]
            p1 -= 1
        else:
            nums1[p] = nums2[p2]
            p2 -= 1
        p -= 1
    while p2 >= 0:
        nums1[p] = nums2[p2]
        p2 -= 1
        p -= 1
```

**Key insight:** Three pointers from right avoids data loss

**Related problems:** #21 (Merge Linked Lists), #977 (Squares of Sorted Array)

---

**Complexity Analysis**
- **Time:** O((m+n)log(m+n))
- **Space:** O(1)

**What I Learned**
- In-place modification with extra space
- Why two-pointer from ends prevents data loss
- Trade-offs between simplicity and optimization
