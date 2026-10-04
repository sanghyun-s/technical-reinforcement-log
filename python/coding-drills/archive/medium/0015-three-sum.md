# 0015. Three Sum

**Difficulty:** Medium  
**Pattern:** Two Pointers  
**Date Solved:** 2026-05-15  
**Acceptance Rate:** 39.0%  
**Status:** ✅

---

## Understanding the Goal

Find all unique triplets that sum to 0. Avoid duplicate triplets in result.

**Key challenge:** Handle duplicates without creating duplicate results.

**Examples Visualized:**

```
nums = [-1,0,1,2,-1,-4]
After sort: [-4,-1,-1,0,1,2]

Triplet 1: [-1, -1, 2] = 0 ✓
Triplet 2: [-1, 0, 1] = 0 ✓

(Don't include [-1,0,1] again)
```

---

## LAYER 1: Line-by-Line Explanation

```python
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        res = []
        nums.sort()  # Enable two-pointer + skip duplicates
        
        # Fix first number, find two that sum to -(first)
        for i in range(len(nums)):
            # Optimization: if positive, no triplet possible
            if nums[i] > 0:
                break
            
            # Skip duplicate first numbers
            if i == 0 or nums[i - 1] != nums[i]:
                self.twoSumII(nums, i, res)
        
        return res
    
    def twoSumII(self, nums: List[int], i: int, res: List[List[int]]):
        # Find two in nums[i+1:] that sum to -nums[i]
        lo = i + 1
        hi = len(nums) - 1
        
        while lo < hi:
            sum = nums[i] + nums[lo] + nums[hi]
            
            if sum < 0:
                lo += 1
            elif sum > 0:
                hi -= 1
            else:
                # Found triplet!
                res.append([nums[i], nums[lo], nums[hi]])
                lo += 1
                hi -= 1
                
                # Skip duplicate lo values
                while lo < hi and nums[lo] == nums[lo - 1]:
                    lo += 1
```

---

## LAYER 2: Worked Examples

**Example 1: [-1,0,1,2,-1,-4]**
```
After sort: [-4,-1,-1,0,1,2]

i=0, nums[0]=-4: Find two that sum to 4
  lo=1 (-1), hi=5 (2): -4-1+2=-3 (too small) → lo++
  lo=2 (-1), hi=5 (2): -4-1+2=-3 (too small) → lo++
  lo=3 (0), hi=5 (2): -4+0+2=-2 (too small) → lo++
  ...no solution

i=1, nums[1]=-1: Find two that sum to 1
  lo=2 (-1), hi=5 (2): -1-1+2=0 (too small) → lo++
  lo=3 (0), hi=5 (2): -1+0+2=1 (too big) → hi--
  lo=3 (0), hi=4 (1): -1+0+1=0 ✓ → append [-1,0,1]
  lo++, hi--
  lo=4, hi=3: exit

i=2, nums[2]=-1: Skip (nums[1]==-1, duplicate)

i=3, nums[3]=0: Find two that sum to 0
  lo=4 (1), hi=5 (2): 0+1+2=3 (too big) → hi--
  lo=4, hi=4: exit

Result: [[-1,0,1]]
```

**Example 2: [0,1,1]**
```
i=0, nums[0]=0: Find two that sum to 0
  lo=1 (1), hi=2 (1): 0+1+1=2 (too big) → hi--
  lo=1, hi=1: exit

Result: []  ✓
```

---

## LAYER 3: Key Insights

**Pattern:** Two-Pointer with Duplicate Handling

| Aspect | Value |
|--------|-------|
| **Time** | O(n²) - outer O(n), inner O(n) |
| **Space** | O(1) excluding output |
| **Sort benefit** | Enables two-pointer + easy duplicate detection |

**Common mistakes:**
- ❌ Not sorting (can't use two-pointer)
- ❌ Forgetting to skip duplicates (duplicate triplets in result)
- ❌ Not checking `i==0` condition
- ❌ Not skipping duplicates in inner loop

**When to use:**
- Find multiple pairs/triplets with target sum
- Need O(n²) acceptable
- Duplicates must be handled

---

## LAYER 4: Interview Variations

• **If question asks "closest sum to target"** → Track min difference instead of checking `sum==0`, return best triplet

• **If question says "4-sum or k-sum"** → Fix multiple numbers with nested loops, then two-pointer on remaining, skip duplicates at all levels

• **If question asks "count triplets instead of listing"** → Increment counter instead of appending, same logic

• **If question limits to "one triplet only"** → Return immediately after finding first (no need to continue)

---

## LAYER 5: Cheat Sheet Entry

**Pattern:** N-Sum with Two-Pointer

**Template:**
```python
def threeSum(nums):
    nums.sort()
    res = []
    for i in range(len(nums)):
        if nums[i] > 0:
            break
        if i == 0 or nums[i-1] != nums[i]:
            lo, hi = i+1, len(nums)-1
            while lo < hi:
                total = nums[i] + nums[lo] + nums[hi]
                if total < 0:
                    lo += 1
                elif total > 0:
                    hi -= 1
                else:
                    res.append([nums[i], nums[lo], nums[hi]])
                    lo += 1
                    hi -= 1
                    while lo < hi and nums[lo] == nums[lo-1]:
                        lo += 1
    return res
```

**Key insight:** Sort enables two-pointer + duplicate skipping

**Related problems:** #16 (3Sum Closest), #18 (4Sum), #259 (3Sum Smaller)

---

**Complexity Analysis**
- **Time:** O(n²)
- **Space:** O(1)

**What I Learned**
- Combining sorting with two-pointer technique
- Duplicate handling at multiple levels
- How to approach N-sum problems systematically
