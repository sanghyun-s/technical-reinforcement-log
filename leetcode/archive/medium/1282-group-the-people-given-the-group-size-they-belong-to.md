# 1282. Group the People Given the Group Size They Belong To

**Difficulty:** Medium  
**Pattern:** Greedy / Grouping  
**Date Solved:** 2026-05-18  
**Status:** ✅

---

## Understanding the Goal

Group people so each person is in a group of the exact size they require.

**Key insight:** Group people with same required size, then split into exact-sized groups greedily.

---

## LAYER 1: Line-by-Line Code Explained

```python
def groupThePeople(self, groupSizes: List[int]) -> List[List[int]]:
    # Dictionary to group people by their required group size
    # Key: group size, Value: list of person IDs with that size requirement
    size_to_people = {}
    
    # Step 1: Organize people by their required group size
    for person_id, size in enumerate(groupSizes):
        if size not in size_to_people:
            size_to_people[size] = []
        size_to_people[size].append(person_id)
    
    result = []
    
    # Step 2: For each group size, form groups greedily
    for size, people in size_to_people.items():
        # Split people list into chunks of 'size'
        # When we have 'size' people, form a group and move to next
        for i in range(0, len(people), size):
            result.append(people[i:i+size])
    
    return result
```

**Logic:**
1. Group people by required size (hash map)
2. For each size bucket, split into groups of that size
3. Greedy: take first `size` people, form group, repeat

---

## LAYER 2: Worked Examples

**Example 1: groupSizes = [3,3,3,3,3,1,3]**

```
Step 1: Organize by size
  size 1: [5]          (person 5 needs group of 1)
  size 3: [0,1,2,3,4,6] (persons 0,1,2,3,4,6 need groups of 3)

Step 2: Form groups greedily
  Size 1 bucket: [5]
    - Take first 1: [5] → form group
    - Done (0 remaining)
  
  Size 3 bucket: [0,1,2,3,4,6]
    - Take first 3: [0,1,2] → form group
    - Take next 3: [3,4,6] → form group
    - Done (0 remaining)

Result: [[5], [0,1,2], [3,4,6]] ✓
```

**Example 2: groupSizes = [2,1,3,3,3,2]**

```
Step 1: Organize by size
  size 1: [1]          (person 1)
  size 2: [0,5]        (persons 0,5)
  size 3: [2,3,4]      (persons 2,3,4)

Step 2: Form groups greedily
  Size 1: [1]
    - Take first 1: [1] → form group
  
  Size 2: [0,5]
    - Take first 2: [0,5] → form group
  
  Size 3: [2,3,4]
    - Take first 3: [2,3,4] → form group

Result: [[1], [0,5], [2,3,4]] ✓
```

**Why greedy works:**

```
Problem guarantees valid solution exists, so:
- If person needs group of size k, will have k-1 others of same size
- Greedy: just accumulate and form groups as we reach the size
- Order doesn't matter (any valid grouping works)
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(n) - iterate once to organize, once to group |
| **Space** | O(n) - hash map stores all people |
| **Greedy approach** | Take first `size` people from bucket, form group |
| **Why it works** | Problem guarantees valid solution exists |

**Key observation:**
- If person needs group of size k, must be with k-1 others of same size
- Greedy: just accumulate and form groups as we reach the size
- Order doesn't matter (any valid grouping works)

---

## LAYER 4: Interview Variations

• **If asked "minimize number of groups"** → Same solution (already minimal)  
• **If asked "how many groups of each size"** → Track count while forming  
• **If asked "verify solution is valid"** → Check each group has exact size  
• **If asked "what if groupSizes can't be perfectly grouped"** → Use overflow handling  

---

## LAYER 5: Cheat Sheet

```python
def groupThePeople(self, groupSizes):
    # Organize people by required group size
    size_to_people = {}
    for person_id, size in enumerate(groupSizes):
        if size not in size_to_people:
            size_to_people[size] = []
        size_to_people[size].append(person_id)
    
    result = []
    
    # For each size, greedily form groups
    for size, people in size_to_people.items():
        for i in range(0, len(people), size):
            result.append(people[i:i+size])
    
    return result
```

**Alternative using defaultdict:**
```python
from collections import defaultdict

def groupThePeople(self, groupSizes):
    size_to_people = defaultdict(list)
    
    for person_id, size in enumerate(groupSizes):
        size_to_people[size].append(person_id)
    
    result = []
    for size, people in size_to_people.items():
        for i in range(0, len(people), size):
            result.append(people[i:i+size])
    
    return result
```

---

**Time:** O(n) | **Space:** O(n)
