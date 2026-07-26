# 0027. Remove Element

**Difficulty:** Easy  
**Pattern:** Two Pointers  
**Date Solved:** 2026-05-15  
**Acceptance Rate:** 61.8%  
**Status:** ✅

---

## Understanding the Goal

Remove all occurrences of a specific value. Return count of remaining elements.

**Key insight:** Filter elements, keep only non-target values. Two pointers: read scans, write places keepers.

**Examples Visualized:**

```
nums = [3,2,2,3], val = 3

Write pointer places non-3 elements at front
Result: [2,2,_,_] with k=2 (first 2 elements are non-3)
```

---

## LAYER 1: Line-by-Line Explanation

```python
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        # Write pointer (place next non-target element)
        i = 0
        
        # Read pointer (scan all elements)
        for j in range(len(nums)):
            # If element is not our target value, keep it
            if nums[j] != val:
                # Place keeper at write position
                nums[i] = nums[j]
                
                # Move write pointer for next keeper
                i += 1
        
        # Return count of non-target elements
        return i
```

---

## LAYER 2: Worked Examples

**Example 1: [3,2,2,3], val=3**
```
i = 0

j=0: nums[0]=3 == val? YES, skip
j=1: nums[1]=2 == val? NO
     nums[0] = 2, i = 1
j=2: nums[2]=2 == val? NO
     nums[1] = 2, i = 2
j=3: nums[3]=3 == val? YES, skip

Result: [2,2,_,_] with k=2 ✓
```

**Example 2: [0,1,2,2,3,0,4,2], val=2**
```
i = 0

j=0: 0 != 2? YES → nums[0]=0, i=1
j=1: 1 != 2? YES → nums[1]=1, i=2
j=2: 2 != 2? NO
j=3: 2 != 2? NO
j=4: 3 != 2? YES → nums[2]=3, i=3
j=5: 0 != 2? YES → nums[3]=0, i=4
j=6: 4 != 2? YES → nums[4]=4, i=5
j=7: 2 != 2? NO

Result: [0,1,3,0,4,_,_,_] with k=5 ✓
```

---

## LAYER 3: Key Insights

**Pattern:** In-Place Filtering (Remove Specific Value)

| Aspect | Value |
|--------|-------|
| **Time** | O(n) - single pass |
| **Space** | O(1) - in-place |
| **Condition** | Check `!= val` for filtering |

**Common mistakes:**
- ❌ Not advancing write pointer correctly
- ❌ Overwriting data before reading
- ❌ Using `==` instead of `!=`

**When to use:**
- Remove specific value from array
- Order doesn't matter (array permutation allowed)
- In-place modification required

---

## LAYER 4: Interview Variations

• **If question asks "remove multiple values"** → Change condition to `if nums[j] not in [val1, val2, ...]` or use set

• **If question adds "remove values > k"** → Use condition `if nums[j] <= k` to keep values <= k

• **If question says "preserve order critical"** → Must use two-pointer (order preserved), not swap

• **If question asks "in-place removal without extra space"** → Two-pointer from ends (swap approach) for efficiency with many removals

---

## LAYER 5: Cheat Sheet Entry

**Pattern:** In-Place Filtering

**Template:**
```python
def removeElement(nums, val):
    i = 0
    for j in range(len(nums)):
        if nums[j] != val:
            nums[i] = nums[j]
            i += 1
    return i
```

**Key insight:** Write pointer only advances when keeping element

**Related problems:** #26 (Remove Duplicates), #283 (Move Zeroes), #80 (Remove Duplicates II)

---

**Complexity Analysis**
- **Time:** O(n)
- **Space:** O(1)

**What I Learned**
- Two-pointer for filtering (not just sorting)
- Difference between in-place and creating new array
- Why order preservation matters for choice of approach
