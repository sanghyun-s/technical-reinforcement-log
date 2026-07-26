# 2545. Sort the Students by Their Kth Score

**Difficulty:** Medium  
**Pattern:** Sorting / Matrix  
**Date Solved:** 2026-05-15  
**Status:** ✅

---

## Understanding the Goal

Sort matrix rows by score at column k, from highest to lowest.

**Key insight:** Use Python's `sorted()` with custom key function to sort rows by kth element.

---

## LAYER 1: Line-by-Line Explanation

```python
def sortTheStudents(self, score: List[List[int]], k: int) -> List[List[int]]:
    # Sort rows by their kth element in descending order
    # key=lambda row: row[k] extracts the sorting value
    # reverse=True sorts highest to lowest
    return sorted(score, key=lambda row: row[k], reverse=True)
```

---

## LAYER 2: Worked Examples

**score=[[10,6,9,1],[7,5,11,2],[4,8,3,15]], k=2:**
```
Kth scores: [9, 11, 3]
Sorted by k descending: 11, 9, 3
Result: [[7,5,11,2], [10,6,9,1], [4,8,3,15]] ✓
```

**score=[[3,4],[5,6]], k=0:**
```
Kth scores: [3, 5]
Sorted descending: [5,6], [3,4] ✓
```

---

## LAYER 3: Key Insights

| Aspect | Value |
|--------|-------|
| **Time** | O(m log m) where m = rows |
| **Space** | O(m) for sorted result |
| **Key function** | `lambda row: row[k]` |

**Why it works:** Lambda extracts kth element for comparison.

---

## LAYER 4: Interview Variations

• **Ascending instead** → Remove `reverse=True`  
• **Sort by multiple columns** → `key=lambda row: (-row[k], -row[j])`  
• **Return indices only** → `sorted(range(len(score)), key=lambda i: score[i][k], reverse=True)`  
• **Modify in-place** → Use `.sort()` instead of `sorted()`  

---

## LAYER 5: Cheat Sheet

```python
def sortTheStudents(score, k):
    return sorted(score, key=lambda row: row[k], reverse=True)
```

**Key:** Lambda function extracts sorting criterion from rows.

---

**Time:** O(m log m) | **Space:** O(m)
