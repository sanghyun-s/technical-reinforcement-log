# 0026. Remove Duplicates from Sorted Array

**Difficulty:** Easy  
**Pattern:** Two Pointers  
**Date Solved:** 2026-05-15  
**Acceptance Rate:** 62.8%  
**Status:** ✅

---

## Understanding the Goal

Remove duplicates in-place from sorted array. Return count of unique elements.

**Key insight:** Two pointers - one reads, one writes. Since sorted, unique elements come in sequence.

**Examples Visualized:**

```
nums = [1,1,2,2,3]

Two pointers approach:
write pointer moves only when new element found
Result: [1,2,3,_,_] with k=3 (first 3 are unique)
```

---

## LAYER 1: Line-by-Line Explanation

```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        # Write pointer (where to place next unique element)
        insertIndex = 1
        
        # Read pointer (scan through array)
        for i in range(1, len(nums)):
            # If current different from previous = new unique element
            if nums[i - 1] != nums[i]:
                # Place unique element at write position
                nums[insertIndex] = nums[i]
                
                # Move write pointer for next unique
                insertIndex += 1
        
        # Return count of unique elements
        return insertIndex
```

---

## LAYER 2: Worked Examples

**Example 1: [1,1,2]**
```
insertIndex = 1

i=1: nums[0]=1 != nums[1]=1? NO, skip
i=2: nums[1]=1 != nums[2]=2? YES
     nums[1] = nums[2] = 2
     insertIndex = 2

Result: [1,2,_] with k=2 ✓
```

**Example 2: [0,0,1,1,1,2,2,3,3,4]**
```
insertIndex = 1

i=1: 0 != 0? NO
i=2: 0 != 1? YES → nums[1]=1, insertIndex=2
i=3: 1 != 1? NO
i=4: 1 != 1? NO
i=5: 1 != 2? YES → nums[2]=2, insertIndex=3
i=6: 2 != 2? NO
i=7: 2 != 3? YES → nums[3]=3, insertIndex=4
i=8: 3 != 3? NO
i=9: 3 != 4? YES → nums[4]=4, insertIndex=5

Result: [0,1,2,3,4,_,_,_,_,_] with k=5 ✓
```

---

## LAYER 3: Key Insights

**Pattern:** Two-Pointer In-Place Modification

| Aspect | Value |
|--------|-------|
| **Time** | O(n) - single pass |
| **Space** | O(1) - no extra space |
| **Pointers** | Read moves every iteration, write moves only on change |

**Common mistakes:**
- ❌ Comparing wrong elements (should compare adjacent)
- ❌ Not using insertIndex correctly
- ❌ Forgetting to increment insertIndex

**When to use:**
- Remove duplicates from sorted array
- In-place array modification
- When extra space not allowed

---

## LAYER 4: Interview Variations

• **If question says "remove value k instead of duplicates"** → Check `if nums[i] != k` instead of comparing adjacent, place non-k values at write pointer

• **If question allows removing at most m duplicates"** → Track count of current element, only write if count <= m

• **If question asks "unsorted array"** → Use hash set to track seen elements, but requires O(n) space

• **If question says "return new array"** → Create new list instead of modifying in-place

---

## LAYER 5: Cheat Sheet Entry

**Pattern:** In-Place Array Filtering

**Template:**
```python
def removeDuplicates(nums):
    insertIndex = 1
    for i in range(1, len(nums)):
        if nums[i-1] != nums[i]:
            nums[insertIndex] = nums[i]
            insertIndex += 1
    return insertIndex
```

**Key insight:** Write pointer only advances when new value found

**Related problems:** #80 (Remove Duplicates II), #27 (Remove Element), #283 (Move Zeroes)

---

**Complexity Analysis**
- **Time:** O(n)
- **Space:** O(1)

**What I Learned**
- Two-pointer for in-place modifications
- Why sorted array enables this efficiency
- Difference between read and write pointers
