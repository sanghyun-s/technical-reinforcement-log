# 1874. Minimize Product Sum of Two Arrays

**Difficulty:** Medium  
**Pattern:** Greedy / Sorting  
**Date Solved:** 2026-05-18  
**Status:** ✅

---

## Understanding the Goal

Minimize product sum by rearranging nums1. Each element of nums1 multiplies with corresponding element of nums2.

**Key insight:** Pair smallest with largest to minimize products.

---

## LAYER 1: Editorial Code Explained

```python
def minProductSum(self, nums1: List[int], nums2: List[int]) -> int:
    # Sort nums1 ascending (smallest first)
    nums1.sort()
    # Sort nums2 descending (largest first)
    nums2.sort(reverse=True)
    
    # Initialize sum
    ans = 0
    
    # Pair elements: smallest from nums1 with largest from nums2
    for num1, num2 in zip(nums1, nums2):
        ans += num1 * num2
    
    return ans
```

**Logic:**
1. Sort nums1 ascending: [smallest, ..., largest]
2. Sort nums2 descending: [largest, ..., smallest]
3. Pair them: smallest×largest, next_smallest×next_largest, ...
4. Sum all products

---

## LAYER 2: Worked Examples

**Example 1: nums1=[5,3,4,2], nums2=[4,2,2,5]**

```
Sort nums1 ascending: [2,3,4,5]
Sort nums2 descending: [5,4,2,2]

Pairing:
  2 × 5 = 10
  3 × 4 = 12
  4 × 2 = 8
  5 × 2 = 10

Sum: 10 + 12 + 8 + 10 = 40 ✓

Why this works:
- Smallest (2) pairs with largest (5)
- 2nd smallest (3) pairs with 2nd largest (4)
- Result: minimize total product
```

**Example 2: nums1=[2,1,4,5,7], nums2=[3,2,4,8,6]**

```
Sort nums1 ascending: [1,2,4,5,7]
Sort nums2 descending: [8,6,4,3,2]

Pairing:
  1 × 8 = 8
  2 × 6 = 12
  4 × 4 = 16
  5 × 3 = 15
  7 × 2 = 14

Sum: 8 + 12 + 16 + 15 + 14 = 65 ✓

Why this minimizes:
- Small numbers don't multiply with large numbers
- Instead, small × large and large × small balance out
```

**Counterexample: What if we DON'T rearrange?**

```
nums1 = [5,3,4,2], nums2 = [4,2,2,5]
Direct pairing (no rearrange):
  5×4 + 3×2 + 4×2 + 2×5 = 20 + 6 + 8 + 10 = 44

With our greedy rearrange: 40
40 < 44 ✓ (Better!)
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(n log n) - sorting dominates |
| **Space** | O(1) - no extra space needed |
| **Strategy** | Pair opposites: smallest with largest |
| **Why it works** | Minimize large products |

**Mathematical intuition:**

For minimizing sum of products with fixed sets:
- Don't pair large with large (gets very big)
- Don't pair small with small (wastes small multiplier)
- Instead: pair small with large, medium with medium
- This balances all products downward

**Rearrangement inequality:**
- If a₁ ≤ a₂ ≤ ... ≤ aₙ and b₁ ≤ b₂ ≤ ... ≤ bₙ
- Minimum sum = a₁bₙ + a₂bₙ₋₁ + ... + aₙb₁ (opposite order)
- This is EXACTLY what we do!

---

## LAYER 4: Interview Variations

• **If asked "maximum product sum"** → Pair small with small, large with large (same order)  
• **If asked "why this works"** → Explain rearrangement inequality  
• **If asked "prove it's optimal"** → Show counterexample with wrong pairing  
• **If only one array is rearrangeable** → Still sort both, apply same logic  

---

## LAYER 5: Cheat Sheet

```python
def minProductSum(self, nums1, nums2):
    nums1.sort()
    nums2.sort(reverse=True)
    return sum(a * b for a, b in zip(nums1, nums2))
```

**Why it works:**
- nums1 ascending: small → large
- nums2 descending: large → small
- Pairing: smallest×largest, 2nd smallest×2nd largest, ...
- Result: minimized product sum

**For maximum:**
```python
# Just sort both ascending (or both descending)
nums1.sort()
nums2.sort()
return sum(a * b for a, b in zip(nums1, nums2))
```

---

**Time:** O(n log n) | **Space:** O(1)
