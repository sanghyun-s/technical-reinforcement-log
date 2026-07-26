# 2160. Minimum Sum of Four Digit Number After Splitting Digits

**Difficulty:** Easy  
**Pattern:** Greedy / Digit Manipulation  
**Date Solved:** 2026-05-18  
**Status:** ✅

---

## Understanding the Goal

Split 4-digit number into two numbers to minimize their sum.

**Key insight:** To minimize sum, create two 2-digit numbers (not 3+1). Put smallest digits in tens place, larger in ones place.

---

## LAYER 1: Line-by-Line Code Explained

```python
def minimumSum(self, num: int) -> int:
    # Convert number to string to access individual digits
    digits = sorted(str(num))
    # digits is now sorted in ascending order: [smallest, ..., largest]
    
    # Create two 2-digit numbers with smallest digits in tens place
    # digits[0] and digits[1] are two smallest digits
    # digits[2] and digits[3] are two largest digits
    
    # new1 = (digits[0] * 10) + digits[2]
    # new2 = (digits[1] * 10) + digits[3]
    new1 = int(digits[0] + digits[2])  # String concat, then convert
    new2 = int(digits[1] + digits[3])  # String concat, then convert
    
    return new1 + new2
```

**Logic:**
1. Convert to string and sort digits
2. Two 2-digit numbers are optimal (not 3+1 or 1+3)
3. Put 2 smallest in tens place: digits[0] and digits[1]
4. Put 2 largest in ones place: digits[2] and digits[3]
5. Pair: (digits[0], digits[2]) and (digits[1], digits[3])
6. Sum the two numbers

---

## LAYER 2: Worked Examples

**Example 1: num = 2932**

```
Digits: 2, 9, 3, 2
Sorted: [2, 2, 3, 9]

Smallest two: 2, 2 (tens place)
Largest two: 3, 9 (ones place)

Pairing strategy:
  new1 = (digits[0] in tens) + (digits[2] in ones) = 2*10 + 3 = 23
  new2 = (digits[1] in tens) + (digits[3] in ones) = 2*10 + 9 = 29

Sum: 23 + 29 = 52 ✓

Why this pairing?
- If we do 22 + 39 = 61 (worse)
- If we do 23 + 29 = 52 ✓ (best)
```

**Example 2: num = 4009**

```
Digits: 4, 0, 0, 9
Sorted: [0, 0, 4, 9]

Smallest two: 0, 0 (tens place)
Largest two: 4, 9 (ones place)

Pairing:
  new1 = 0*10 + 4 = 4
  new2 = 0*10 + 9 = 9

Sum: 4 + 9 = 13 ✓

Why this works with leading zeros:
- 0 in tens place still contributes (0*10 = 0)
- Allows us to use all digits
```

**Why 2-digit + 2-digit is optimal:**

```
Given 4 digits: [a, b, c, d] where a ≤ b ≤ c ≤ d

Option 1: Two 2-digit numbers
  Best pairing: (10a + c) + (10b + d)
  = 10a + 10b + c + d
  = 10(a+b) + (c+d)

Option 2: One 3-digit, one 1-digit
  Best: (100a + 10b + c) + d
  = 100a + 10b + c + d
  
Comparing:
  Option 1: 10(a+b) + (c+d) = 10a + 10b + c + d
  Option 2: 100a + 10b + c + d
  
  Difference: Option2 - Option1 = 90a > 0
  
  So Option 1 (two 2-digit) is always better! ✓

Option 3: One 1-digit, one 3-digit
  Same analysis - worse than Option 1
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(1) - always 4 digits, sorting is O(4 log 4) = O(1) |
| **Space** | O(1) - only storing a few variables |
| **Key insight** | 2-digit + 2-digit minimizes, not 3+1 or 1+3 |
| **Pairing strategy** | Smallest digits in tens, largest in ones |

**Why pairing matters:**
- Tens place has coefficient 10
- Ones place has coefficient 1
- Put smallest digits where they're multiplied by 10
- Put largest digits where they're multiplied by 1

---

## LAYER 4: Interview Variations

• **If asked "maximize sum"** → Put largest in tens place  
• **If asked "different number of digits"** → Generalize pairing strategy  
• **If asked "construct the numbers"** → Return [new1, new2] instead  
• **If asked "with n digits"** → Think about optimal digit distribution  

---

## LAYER 5: Cheat Sheet

```python
def minimumSum(self, num: int) -> int:
    digits = sorted(str(num))
    new1 = int(digits[0] + digits[2])
    new2 = int(digits[1] + digits[3])
    return new1 + new2
```

**Alternative using arithmetic:**
```python
def minimumSum(self, num: int) -> int:
    digits = sorted(str(num))
    return (int(digits[0])*10 + int(digits[2])) + (int(digits[1])*10 + int(digits[3]))
```

**One-liner:**
```python
def minimumSum(self, num: int) -> int:
    d = sorted(str(num))
    return int(d[0] + d[2]) + int(d[1] + d[3])
```

---

**Time:** O(1) | **Space:** O(1)
