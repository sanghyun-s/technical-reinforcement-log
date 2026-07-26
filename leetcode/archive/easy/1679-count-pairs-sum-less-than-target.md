# 1679. Count Pairs Whose Sum is Less than Target

**Difficulty:** Easy  
**Pattern:** Two Pointers / Sorting  
**Date Solved:** 2026-05-15  
**Status:** ✅

---

## Understanding the Goal

Count pairs (i,j) where i<j and nums[i] + nums[j] < target.

**Key insight:** Sort, then use two pointers. When sum < target, **all pairs between left and right satisfy condition**.

---

## LAYER 1: Line-by-Line Explanation

```python
def countPairs(self, nums: List[int], target: int) -> int:
    nums.sort()
    left, right, count = 0, len(nums) - 1, 0
    
    while left < right:
        if nums[left] + nums[right] < target:
            # All pairs (left,right), (left,right-1), ... are valid
            count += right - left
            left += 1
        else:
            right -= 1
    
    return count
```

---

## LAYER 2: Worked Examples

**[-1,1,2,3,1], target=2:**
```
Sort: [-1,1,1,2,3]
left=0 (-1), right=3 (2): -1+2=1 < 2 ✓
  count += 3-0 = 3 pairs: (-1,2), (-1,1), (-1,1)
```

**[-6,2,5,-2,-7,-1,3], target=-2:**
```
Sort: [-7,-6,-2,-1,2,3,5]
left=0, right=6: -7+5=-2 ✗ → right--
left=0, right=5: -7+3=-4 < -2 ✓
  count += 5 pairs
... (continue logic)
Total: 10 ✓
```

---

## LAYER 3: Key Insights

| Aspect | Value |
|--------|-------|
| **Time** | O(n log n) - sort + two pointers |
| **Space** | O(1) - only pointers |
| **Key insight** | Count all pairs at once: `right - left` |

**Why it works:** Sorted array reveals structure - when left+right < target, ALL between satisfy it.

---

## LAYER 4: Interview Variations

• **Sum > target instead** → Reverse logic: count pairs that DON'T satisfy  
• **Sum in range [a,b]** → Two passes, subtract results  
• **Find all pairs** → Collect instead of counting  
• **Exact sum == target** → Use hash map approach  

---

## LAYER 5: Cheat Sheet

```python
def countPairs(nums, target):
    nums.sort()
    left, right, count = 0, len(nums) - 1, 0
    while left < right:
        if nums[left] + nums[right] < target:
            count += right - left
            left += 1
        else:
            right -= 1
    return count
```

**Key:** After sort, counting multiple pairs at once.

---

**Time:** O(n log n) | **Space:** O(1)
