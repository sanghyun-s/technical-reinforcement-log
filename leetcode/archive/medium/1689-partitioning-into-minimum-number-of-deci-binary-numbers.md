# 1689. Partitioning Into Minimum Number Of Deci-Binary Numbers

**Difficulty:** Medium  
**Pattern:** Greedy / Math  
**Date Solved:** 2026-05-18  
**Status:** ✅

---

## Understanding the Goal

Find minimum deci-binary numbers that sum to n. A deci-binary number has only 0s and 1s.

**Key insight:** The answer is the maximum digit in n. Why? Each deci-binary can contribute at most 1 to each digit position.

---

## LAYER 1: Editorial Code Explained

```python
def minPartitions(self, n: str) -> int:
    # The answer is simply the maximum digit in n
    return int(max(n))
```

**Logic:**
- Deci-binary numbers have only 0s and 1s
- To form digit d at any position, we need d deci-binary numbers contributing 1 at that position
- Maximum digit in n = minimum deci-binary numbers needed

---

## LAYER 2: Worked Examples

**Example 1: n = "32"**

```
Digits: 3, 2
Maximum digit: 3

Why 3?
Position 0: need to make "2" → 2 deci-binary numbers contribute 1
Position 1: need to make "3" → 3 deci-binary numbers contribute 1

We need at least 3 deci-binary numbers:
  10 (first contributes 1 to position 0)
  11 (first and second contribute 1 to position 1)
  11 (first and third contribute 1 to position 1)
Sum: 10 + 11 + 11 = 32 ✓

Answer: 3 (the maximum digit)
```

**Example 2: n = "82734"**

```
Digits: 8, 2, 7, 3, 4
Maximum digit: 8

Why 8?
Position 0: need "8" → 8 numbers contribute 1
Position 1: need "2" → 2 numbers contribute 1
Position 2: need "7" → 7 numbers contribute 1
Position 3: need "3" → 3 numbers contribute 1
Position 4: need "4" → 4 numbers contribute 1

We need 8 deci-binary numbers (enough for the maximum digit 8)
The remaining positions are covered by subsets of those 8

Answer: 8
```

**Example 3: n = "27346209830709182346"**

```
Find max digit: 9
Answer: 9

(Each position's digit is covered by a subset of 9 deci-binary numbers)
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(n) - iterate through digits once |
| **Space** | O(1) - only storing max |
| **Key insight** | Max digit = minimum deci-binary count |
| **Why it works** | Each deci-binary contributes ≤1 per position |

**Mathematical proof:**
- Deci-binary: each digit is 0 or 1
- To sum to digit d at position i, need d contributions of 1
- Max digit across all positions = bottleneck
- Therefore: answer = max digit

---

## LAYER 4: Interview Variations

• **If asked "construct the deci-binary numbers"** → Use greedy fill from max down  
• **If asked "why is max the answer"** → Explain bottleneck logic  
• **If asked "verify your answer"** → Show they sum correctly  

---

## LAYER 5: Cheat Sheet

```python
def minPartitions(self, n: str) -> int:
    return int(max(n))
```

**That's it!** The solution is just finding the maximum digit.

**Why this works:**
- Deci-binary has only 0s and 1s
- Each position's digit = count of deci-binary numbers with 1 there
- Maximum digit across all positions = minimum deci-binary numbers needed

---

**Time:** O(n) | **Space:** O(1)
