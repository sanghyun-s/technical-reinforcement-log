# 0046. Permutations

**Difficulty:** Medium  
**Pattern:** Backtracking  
**Date Solved:** 2026-05-15  
**Acceptance Rate:** 81.9%  
**Status:** ✅

---

## Understanding the Goal

Generate all possible orderings (permutations) of numbers. Order matters, each number used exactly once.

**Key difference from Combinations:** [1,2] and [2,1] are different permutations.

**Examples Visualized:**

```
nums = [1,2,3]

All 3! = 6 permutations:
[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], [3,2,1]
```

---

## LAYER 1: Line-by-Line Explanation

```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        ans = []
        
        def backtrack(curr):
            # Base case: completed one full permutation
            if len(curr) == len(nums):
                # Make copy (avoid reference issues)
                ans.append(curr[:])
                return
            
            # Try adding each number from nums
            for num in nums:
                # Only add if not already in current permutation
                # Prevents using same number twice in one permutation
                if num not in curr:
                    # Add number
                    curr.append(num)
                    
                    # Recurse to fill remaining positions
                    backtrack(curr)
                    
                    # Backtrack: remove to try next
                    curr.pop()
        
        backtrack([])
        return ans
```

---

## LAYER 2: Worked Examples

**Example 1: nums=[1,2,3]**
```
backtrack([])
  num=1: 1 not in []? YES
    curr=[1]
    backtrack([1])
      num=1: 1 in [1]? YES, skip
      num=2: 2 in [1]? NO
        curr=[1,2]
        backtrack([1,2])
          num=1: YES, skip
          num=2: YES, skip
          num=3: 3 in [1,2]? NO
            curr=[1,2,3]
            backtrack([1,2,3])
              len==3? YES → append [1,2,3] ✓
      num=3: 3 in [1]? NO
        curr=[1,3]
        backtrack([1,3])
          num=1: YES, skip
          num=2: 2 in [1,3]? NO
            curr=[1,3,2]
            backtrack([1,3,2])
              len==3? YES → append [1,3,2] ✓
  
  num=2: 2 not in []? YES
    ... (continues for [2,1,3], [2,3,1])
  
  num=3: 3 not in []? YES
    ... (continues for [3,1,2], [3,2,1])

Result: [[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], [3,2,1]] ✓
```

**Example 2: nums=[0,1]**
```
backtrack([])
  num=0: curr=[0]
    backtrack([0])
      num=0: YES, skip
      num=1: curr=[0,1]
        backtrack([0,1])
          len==2? YES → append [0,1] ✓
  
  num=1: curr=[1]
    backtrack([1])
      num=0: curr=[1,0]
        backtrack([1,0])
          len==2? YES → append [1,0] ✓

Result: [[0,1], [1,0]] ✓
```

---

## LAYER 3: Key Insights

**Pattern:** Backtracking to Generate All Permutations

| Aspect | Value |
|--------|-------|
| **Time** | O(n! × n) - n! permutations, each takes O(n) to copy |
| **Space** | O(n) - recursion depth |
| **Check** | `if num not in curr` prevents reuse - O(n) per check |

**Common mistakes:**
- ❌ Not copying with `curr[:]` (reference issues)
- ❌ Forgetting to pop() after recursion
- ❌ Using `if num not in curr` incorrectly (should check membership)
- ❌ Not checking `len(curr) == len(nums)` as base case

**When to use:**
- Generate all orderings (permutations)
- Order matters (unlike combinations)
- No duplicates allowed in result

---

## LAYER 4: Interview Variations

• **If question says "with duplicates allowed in input"** → Sort first, skip duplicate values in loop (if `i > start and nums[i] == nums[i-1]` skip)

• **If question asks "next permutation"** → Use different approach: find rightmost ascent, swap, reverse suffix (O(n) not O(n!))

• **If question limits to "permutations of length k"** → Change base case from `len(curr)==len(nums)` to `len(curr)==k`

• **If question asks "count permutations instead of listing"** → Return `factorial(n)` directly, avoid building all

---

## LAYER 5: Cheat Sheet Entry

**Pattern:** Backtracking for Permutations

**Template:**
```python
def permute(nums):
    ans = []
    def backtrack(curr):
        if len(curr) == len(nums):
            ans.append(curr[:])  # Deep copy
            return
        for num in nums:
            if num not in curr:  # Only use unused numbers
                curr.append(num)
                backtrack(curr)
                curr.pop()  # Backtrack
    
    backtrack([])
    return ans
```

**Key insight:** Check `num not in curr` prevents reusing numbers

**Related problems:** #47 (Permutations II), #60 (Permutation Sequence), #31 (Next Permutation)

---

**Complexity Analysis**
- **Time:** O(n! × n)
- **Space:** O(n)

**What I Learned**
- Backtracking for permutation generation
- Membership checking prevents reuse
- Deep copying essential for storing results
- Why O(n!) time is unavoidable (n! permutations exist)
