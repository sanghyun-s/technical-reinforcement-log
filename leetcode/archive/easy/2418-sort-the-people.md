# 2418. Sort the People

**Difficulty:** Easy  
**Pattern:** Sorting / Hash Map  
**Date Solved:** 2026-05-15  
**Status:** ✅

---

## Understanding the Goal

Sort names by their corresponding heights in descending order (tallest first).

**Key insight:** Create height-to-name mapping, sort heights, then look up names.

---

## LAYER 1: Line-by-Line Explanation

```python
def sortPeople(self, names: List[str], heights: List[int]) -> List[str]:
    # Map height → name
    height_to_name = dict(zip(heights, names))
    
    # Sort heights descending
    sorted_heights = sorted(heights, reverse=True)
    
    # Return names in sorted order
    return [height_to_name[h] for h in sorted_heights]
```

---

## LAYER 2: Worked Examples

**names=["Mary","John","Emma"], heights=[180,165,170]:**
```
Map: {180:"Mary", 165:"John", 170:"Emma"}
Sorted heights: [180,170,165]
Result: ["Mary","Emma","John"] ✓
```

**names=["Alice","Bob","Bob"], heights=[155,185,150]:**
```
Map: {155:"Alice", 185:"Bob", 150:"Bob"}
Sorted heights: [185,155,150]
Result: ["Bob","Alice","Bob"] ✓
```

---

## LAYER 3: Key Insights

| Aspect | Value |
|--------|-------|
| **Time** | O(n log n) - sorting |
| **Space** | O(n) - hash map |
| **Approach** | Separate sort value from return value |

**Why this works:** Hash map O(1) lookup after sorting heights.

---

## LAYER 4: Interview Variations

• **Return tuples (name, height)** → `sorted(zip(names, heights), key=lambda x: x[1], reverse=True)`  
• **Sort names alphabetically then by height** → Use tuple key: `key=lambda x: (x[1], x[0])`  
• **Return as dict** → Build `{name: height}` instead  
• **Heights not distinct** → Use `defaultdict(list)` for multiple names per height  

---

## LAYER 5: Cheat Sheet

```python
def sortPeople(names, heights):
    height_to_name = dict(zip(heights, names))
    sorted_heights = sorted(heights, reverse=True)
    return [height_to_name[h] for h in sorted_heights]
```

**Alternative - Direct zip:**
```python
return [name for name, _ in sorted(zip(names, heights), key=lambda x: x[1], reverse=True)]
```

---

**Time:** O(n log n) | **Space:** O(n)
