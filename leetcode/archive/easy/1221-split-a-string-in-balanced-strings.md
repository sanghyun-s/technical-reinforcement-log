# 1221. Split a String in Balanced Strings

**Difficulty:** Easy  
**Pattern:** Greedy / String  
**Date Solved:** 2026-05-18  
**Status:** ✅

---

## Understanding the Goal

Split string into maximum number of balanced substrings (equal L and R).

**Key insight:** Greedily split whenever you achieve balance - this maximizes count.

---

## LAYER 1: Line-by-Line Code Explained

```python
def balancedStringSplit(self, s: str) -> int:
    # Track balance between L and R
    # balance > 0 means more R, balance < 0 means more L
    balance = 0
    count = 0
    
    # Iterate through each character
    for char in s:
        # R increases balance, L decreases balance
        if char == 'R':
            balance += 1
        else:  # char == 'L'
            balance -= 1
        
        # When balance = 0, we have a balanced substring
        if balance == 0:
            count += 1
    
    return count
```

**Logic:**
1. Track balance: +1 for 'R', -1 for 'L'
2. When balance = 0, we have equal L and R → found a balanced substring
3. Greedily count whenever we hit 0
4. Return total count

---

## LAYER 2: Worked Examples

**Example 1: s = "RLRRLLRLRL"**

```
Position  Char  Balance  Action
0         R     +1       (1 R, 0 L)
1         L     0        ✓ Balanced! "RL" → count = 1
2         R     +1       (1 R, 0 L)
3         R     +2       (2 R, 0 L)
4         L     +1       (2 R, 1 L)
5         L     0        ✓ Balanced! "RRLL" → count = 2
6         R     +1       (1 R, 0 L)
7         L     0        ✓ Balanced! "RL" → count = 3
8         R     +1       (1 R, 0 L)
9         L     0        ✓ Balanced! "RL" → count = 4

Result: 4 ✓
```

**Example 2: s = "RLRRRLLRLL"**

```
Position  Char  Balance  Action
0         R     +1
1         L     0        ✓ Balanced! "RL" → count = 1
2         R     +1
3         R     +2
4         R     +3
5         L     +2
6         L     +1
7         R     +2
8         L     +1
9         L     0        ✓ Balanced! "RRRLLRLL" → count = 2

Result: 2 ✓
```

**Example 3: s = "LLLLRRRR"**

```
Position  Char  Balance  Action
0         L     -1
1         L     -2
2         L     -3
3         L     -4
4         R     -3
5         R     -2
6         R     -1
7         R     0        ✓ Balanced! "LLLLRRRR" → count = 1

Result: 1 ✓
```

**Why greedy maximizes splits:**

```
If we wait longer: "LLLLRRRR" → 1 split
If we split early: impossible (L imbalance until we see enough R)

But with "RLRRLLRLRL":
Greedy: split at each 0 → 4 splits
Waiting: combine groups → fewer splits

Greedy is optimal because:
- Once balanced, splitting is always valid
- Delaying never increases count
- So split immediately when you can
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(n) - single pass through string |
| **Space** | O(1) - only tracking balance and count |
| **Greedy approach** | Split whenever balance = 0 |
| **Why it works** | Earlier splits don't prevent later splits |

**Balance concept:**
- Balance = count(R) - count(L)
- Balance = 0 means count(R) = count(L) → balanced
- Balance > 0 means more R
- Balance < 0 means more L

---

## LAYER 4: Interview Variations

• **If asked "minimum splits"** → Return 1 (always possible to keep whole string)  
• **If asked "unbalanced string"** → Check if final balance = 0  
• **If asked "find split positions"** → Track indices instead of count  
• **If asked "equal A and B instead"** → Replace R/L logic with A/B  

---

## LAYER 5: Cheat Sheet

```python
def balancedStringSplit(self, s: str) -> int:
    balance = 0
    count = 0
    
    for char in s:
        balance += 1 if char == 'R' else -1
        if balance == 0:
            count += 1
    
    return count
```

**One-liner alternative:**
```python
def balancedStringSplit(self, s: str) -> int:
    balance = count = 0
    for char in s:
        balance += 1 if char == 'R' else -1
        count += balance == 0
    return count
```

**Find split positions:**
```python
def balancedStringSplit(self, s: str) -> int:
    balance = 0
    splits = []
    
    for i, char in enumerate(s):
        balance += 1 if char == 'R' else -1
        if balance == 0:
            splits.append(i)  # Index where split happens
    
    return len(splits)
```

---

**Time:** O(n) | **Space:** O(1)
