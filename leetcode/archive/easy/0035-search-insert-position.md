# 0035. Search Insert Position

**Difficulty:** Easy  
**Pattern:** Binary Search  
**Date Solved:** 2026-05-15  
**Acceptance Rate:** 51.2%  
**Status:** ✅

---

## Understanding the Goal

Find target index or insertion position if not found. Maintain sorted order.

**Key insight:** Binary search naturally finds insertion position. `left` pointer ends at correct position.

**Examples Visualized:**

```
nums = [1,3,5,6], target = 5
Binary search finds 5 at index 2 ✓

nums = [1,3,5,6], target = 2
Binary search ends with left=1 (where 2 should go)
Result: [1, 2, 3, 5, 6] ✓
```

---

## LAYER 1: Line-by-Line Explanation

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        # Two pointers for binary search
        left, right = 0, len(nums) - 1
        
        # Standard binary search loop
        while left <= right:
            # Calculate middle
            pivot = (left + right) // 2
            
            # Found it!
            if nums[pivot] == target:
                return pivot
            
            # Target smaller, search left half
            elif target < nums[pivot]:
                right = pivot - 1
            
            # Target larger, search right half
            else:
                left = pivot + 1
        
        # Loop ended: left is insertion position
        # All elements left of left are < target
        # All elements right of left are > target
        return left
```

---

## LAYER 2: Worked Examples

**Example 1: [1,3,5,6], target=5**
```
left=0, right=3
  pivot=1: nums[1]=3, 5 > 3? YES → left=2

left=2, right=3
  pivot=2: nums[2]=5, 5 == 5? YES → return 2 ✓
```

**Example 2: [1,3,5,6], target=2**
```
left=0, right=3
  pivot=1: nums[1]=3, 2 < 3? YES → right=0

left=0, right=0
  pivot=0: nums[0]=1, 2 > 1? YES → left=1

left=1, right=0 (loop ends)
return 1 → Insert at [1, 2, 3, 5, 6] ✓
```

**Example 3: [1,3,5,6], target=7**
```
left=0, right=3
  pivot=1: 7 > 3? YES → left=2

left=2, right=3
  pivot=2: 7 > 5? YES → left=3

left=3, right=3
  pivot=3: 7 > 6? YES → left=4

left=4, right=3 (loop ends)
return 4 → Insert at end [1, 3, 5, 6, 7] ✓
```

---

## LAYER 3: Key Insights

**Pattern:** Binary Search with Insertion Point

| Aspect | Value |
|--------|-------|
| **Time** | O(log n) |
| **Space** | O(1) |
| **Key** | `left` pointer = insertion position |

**Common mistakes:**
- ❌ Using `>` instead of `<=` in while condition
- ❌ Forgetting to handle edge cases (empty, insert at ends)
- ❌ Not understanding why `left` is insertion point

**When to use:**
- Sorted array, need O(log n)
- Find element or insertion position
- Array doesn't support hashing

---

## LAYER 4: Interview Variations

• **If question asks "find first occurrence with duplicates"** → Keep searching left after finding, use binary search on left half, return leftmost index

• **If question says "find in rotated array"** → Identify sorted half first, then binary search within correct half

• **If question asks "closest value instead"** → Track minimum distance during binary search, return closest element

• **If question limits to "return -1 if not found"** → Change final return from `left` to `-1`, only return index if found

---

## LAYER 5: Cheat Sheet Entry

**Pattern:** Binary Search (Find or Insert)

**Template:**
```python
def searchInsert(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        pivot = (left + right) // 2
        if nums[pivot] == target:
            return pivot
        elif target < nums[pivot]:
            right = pivot - 1
        else:
            left = pivot + 1
    return left  # insertion position
```

**Key insight:** `left > right` guarantees `left` is insertion point

**Related problems:** #34 (Find Range), #33 (Rotated Array), #278 (First Bad Version)

---

**Complexity Analysis**
- **Time:** O(log n)
- **Space:** O(1)

**What I Learned**
- Binary search finds insertion point naturally
- Why `left` pointer is always correct position
- Foundation for advanced binary search variants
