# 0066. Plus One

**Difficulty:** Easy  
**Pattern:** Array/Math  
**Date Solved:** 2026-05-15  
**Acceptance Rate:** 49.9%  
**Status:** ✅

---

## Understanding the Goal

Increment big number (array of digits) by 1. Handle carry-over.

**Key insight:** Process right-to-left. Stop when no carry. Handle all-9s case.

**Examples Visualized:**

```
digits = [1,2,3]
Add 1 to rightmost: 3+1=4 (no carry)
Result: [1,2,4] ✓

digits = [9,9,9]
Add 1 to rightmost: 9+1=10 (carry 1)
All digits 9, so all become 0, prepend 1
Result: [1,0,0,0] ✓
```

---

## LAYER 1: Line-by-Line Explanation

```python
class Solution:
    def plusOne(self, digits: List[int]) -> List[int]:
        n = len(digits)
        
        # Process from RIGHT to LEFT (least to most significant)
        for i in range(n):
            # Convert loop index to actual array index (right to left)
            idx = n - 1 - i
            
            # If digit is 9, becomes 0 and carry continues
            if digits[idx] == 9:
                digits[idx] = 0
            else:
                # Not 9: add 1 and we're done (no carry needed)
                digits[idx] += 1
                return digits
        
        # If loop completes, ALL digits were 9
        # Need new digit at front
        return [1] + digits
```

---

## LAYER 2: Worked Examples

**Example 1: [1,2,3]**
```
i=0, idx=2: digits[2]=3 == 9? NO
            digits[2] = 4
            return [1,2,4] ✓
```

**Example 2: [9]**
```
i=0, idx=0: digits[0]=9 == 9? YES
            digits[0] = 0, continue

Loop ends: all were 9
return [1] + [0] = [1,0] ✓
```

**Example 3: [9,9,9]**
```
i=0, idx=2: digits[2]=9 == 9? YES → becomes 0, continue
i=1, idx=1: digits[1]=9 == 9? YES → becomes 0, continue
i=2, idx=0: digits[0]=9 == 9? YES → becomes 0, continue

Loop ends: digits = [0,0,0]
return [1] + [0,0,0] = [1,0,0,0] ✓
```

---

## LAYER 3: Key Insights

**Pattern:** Right-to-Left Carry Propagation

| Aspect | Value |
|--------|-------|
| **Time** | O(n) worst case, O(1) average |
| **Space** | O(1) excluding output |
| **Direction** | Right to left (least significant first) |

**Common mistakes:**
- ❌ Starting from left (wrong direction)
- ❌ Forgetting [1] prefix for all-9s case
- ❌ Not returning early when no carry

**When to use:**
- Increment big numbers represented as arrays
- Handle carry-over logic
- Right-to-left processing needed

---

## LAYER 4: Interview Variations

• **If question asks "add N instead of 1"** → Outer loop to add each digit of N, similar carry logic but more complex

• **If question says "subtract 1"** → Process right-to-left, if digit > 0 subtract 1 and return, if 0 becomes 9 and borrow continues

• **If question adds "input as string"** → Convert to list first, process same way, convert back to string

• **If question asks "add two numbers"** → Two pointers from ends, add digits with carry, build result

---

## LAYER 5: Cheat Sheet Entry

**Pattern:** Big Number Increment/Carry

**Template:**
```python
def plusOne(digits):
    n = len(digits)
    for i in range(n):
        idx = n - 1 - i
        if digits[idx] < 9:
            digits[idx] += 1
            return digits
        digits[idx] = 0
    return [1] + digits
```

**Key insight:** Early exit when no carry, handle all-9s edge case

**Related problems:** #67 (Add Binary), #415 (Add Strings), #989 (Add to Array)

---

**Complexity Analysis**
- **Time:** O(n) worst case, O(1) average
- **Space:** O(1) excluding output array

**What I Learned**
- Right-to-left processing for carries
- Early termination optimization
- Edge case handling for boundaries
