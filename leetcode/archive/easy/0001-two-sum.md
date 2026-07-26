# 0001. Two Sum

**Difficulty:** Easy  
**Pattern:** Hash Map  
**Date Solved:** 2026-05-15  
**Acceptance Rate:** 57.5%  
**Status:** ✅

---

## Understanding the Goal

Find two numbers that sum to target. Return their indices.

**Key insight:** Use hash map for O(1) lookup of complement.

**Examples Visualized:**

```
nums = [2,7,11,15], target = 9

Check 2: Need 7. Seen? NO → store {2: 0}
Check 7: Need 2. Seen? YES at index 0 → return [0, 1] ✓
```

---

## LAYER 1: Line-by-Line Explanation

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        # Hash map to store value → index
        seen = {}
        
        # Single pass through array
        for i, num in enumerate(nums):
            # What complement do we need?
            complement = target - num
            
            # Have we seen it before?
            if complement in seen:
                # Yes! Return both indices
                return [seen[complement], i]
            
            # No, store this number for future lookup
            seen[num] = i
        
        return []  # No solution found
```

---

## LAYER 2: Worked Examples

**Example 1: [2,7,11,15], target=9**
```
i=0, num=2: complement=7, 7 in seen? NO → seen={2:0}
i=1, num=7: complement=2, 2 in seen? YES → return [0,1] ✓
```

**Example 2: [3,2,4], target=6**
```
i=0, num=3: complement=3, 3 in seen? NO → seen={3:0}
i=1, num=2: complement=4, 4 in seen? NO → seen={3:0, 2:1}
i=2, num=4: complement=2, 2 in seen? YES → return [1,2] ✓
```

---

## LAYER 3: Key Insights

**Pattern:** Hash Map Lookup

| Aspect | Value |
|--------|-------|
| **Time** | O(n) - single pass |
| **Space** | O(n) - hash map storage |
| **Approach** | One pass with hash map |

**Common mistakes:**
- ❌ Using nested loops (O(n²))
- ❌ Not handling target = num*2 case
- ❌ Returning indices in wrong order

**When to use:**
- Need fast lookups in array
- Find pairs with target sum
- Complement technique applies

---

## LAYER 4: Interview Variations

• **If question asks "find all pairs instead of one"** → Collect all pairs in list instead of returning immediately, skip duplicates using set

• **If question says "return values not indices"** → Return `[complement, num]` instead of indices, or sort and return

• **If question adds constraint "each element used once only"** → Same approach works (we check before using)

• **If question says "sum to any of multiple targets"** → Build hash map once, then query for each target complement

---

## LAYER 5: Cheat Sheet Entry

**Pattern:** Hash Map for O(1) Lookup

**Template:**
```python
def twoSum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []
```

**Key insight:** One pass, O(1) lookup per element

**Related problems:** #167, #170, #1099

---

**Complexity Analysis**
- **Time:** O(n)
- **Space:** O(n)

**What I Learned**
- Hash maps enable O(1) complement lookup
- One-pass solution vs two-pointer (needs sorting)
- Why this scales better than brute force

✅ Portfolio-ready solution included
