# 0905. Transform Array by Parity

**Difficulty:** Easy  
**Pattern:** Sorting / Array Transformation  
**Date Solved:** 2026-05-15  
**Status:** ✅

---

## Understanding the Goal

Transform array: replace evens with 0, odds with 1, then sort. **Don't actually transform and sort** - just count evens, then build result.

**Key insight:** Result is always `[0, 0, ..., 0, 1, 1, ..., 1]`, so count evens and construct directly.

---

## LAYER 1: Line-by-Line Explanation

```python
def transformArray(self, nums: List[int]) -> List[int]:
    # Count how many even numbers exist
    even_count = sum(1 for num in nums if num % 2 == 0)
    
    # Build result: 0s for evens, 1s for odds
    return [0] * even_count + [1] * (len(nums) - even_count)
```

---

## LAYER 2: Worked Examples

**[4,3,2,1]:** Even count = 2 → `[0,0,1,1]` ✓  
**[1,5,1,4,2]:** Even count = 2 → `[0,0,1,1,1]` ✓

---

## LAYER 3: Key Insights

| Aspect | Value |
|--------|-------|
| **Time** | O(n) - single count pass |
| **Space** | O(n) - result array |
| **Naive** | O(n log n) - transform then sort |
| **Optimized** | O(n) - count and build |

**Why this works:** Sort always produces `[0...0, 1...1]`, so we skip transform + sort, just count.

---

## LAYER 4: Interview Variations

• **Return transformed before sorting** → `[num % 2 for num in nums]`  
• **Descending order** → `[1] * odds + [0] * evens`  
• **Count separately** → Return `(even_count, odd_count)`  
• **Different mapping** → Replace with custom values, count those  

---

## LAYER 5: Cheat Sheet

```python
def transformArray(nums):
    even_count = sum(1 for num in nums if num % 2 == 0)
    return [0] * even_count + [1] * (len(nums) - even_count)
```

**Key:** Skip intermediate transform/sort, go straight to final structure.

---

**Time:** O(n) | **Space:** O(n)
