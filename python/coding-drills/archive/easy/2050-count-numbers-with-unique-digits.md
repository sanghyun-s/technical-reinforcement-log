# 2050. Count Numbers With Unique Digits II

**Difficulty:** Easy  
**Pattern:** Dynamic Programming / Brute Force  
**Date Solved:** 2026-05-15  
**Status:** ✅

---

## Understanding the Goal

Count numbers in range [a,b] where all digits are unique (no repeated digits).

**Key insight:** Check each number if all digits are unique using a set.

---

## LAYER 1: Line-by-Line Explanation

```python
def countNumbers(self, a: int, b: int) -> int:
    count = 0
    for num in range(a, b + 1):
        # Convert to string to access individual digits
        s = str(num)
        # Check if all digits unique: set size == string length
        if len(set(s)) == len(s):
            count += 1
    return count
```

---

## LAYER 2: Worked Examples

**a=1, b=20:**
```
1-9: all unique ✓ (count=9)
10: unique ✓
11: NOT unique ✗ (two 1s)
12-20: all unique ✓
Total: 19 ✓
```

**a=9, b=19:**
```
9,10: unique ✓
11: NOT unique ✗
12-19: unique ✓
Total: 10 ✓
```

---

## LAYER 3: Key Insights

| Aspect | Value |
|--------|-------|
| **Time** | O((b-a) × d) where d ≈ 4 |
| **Space** | O(1) |
| **Key check** | `len(set(str(num))) == len(str(num))` |

**Why set comparison works:** Set removes duplicates. If size doesn't match string length, digits repeated.

---

## LAYER 4: Interview Variations

• **Count repeated digits instead** → Invert: `len(set(...)) != len(...)`  
• **Exactly k unique digits** → Check `len(set(...)) == k`  
• **All even digits** → Add condition: `all(int(d) % 2 == 0 for d in s)`  
• **Digits sorted** → Check `list(s) == sorted(s)`  

---

## LAYER 5: Cheat Sheet

```python
def countNumbers(a, b):
    count = 0
    for num in range(a, b + 1):
        s = str(num)
        if len(set(s)) == len(s):
            count += 1
    return count
```

**One-liner:**
```python
return sum(1 for num in range(a, b+1) if len(set(str(num))) == len(str(num)))
```

---

**Time:** O((b-a) × d) | **Space:** O(1)
