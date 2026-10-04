# 2037. Minimum Number of Moves to Seat Everyone

**Difficulty:** Easy  
**Pattern:** Greedy / Sorting  
**Date Solved:** 2026-05-18  
**Status:** ✅

---

## Understanding the Goal

Assign each student to a seat with minimum total moves. Each move = change position by 1.

**Key insight:** Sort both arrays and pair them in order - this minimizes total distance.

---

## LAYER 1: Editorial Code Explained

```python
def minMovesToSeat(self, seats: List[int], students: List[int]) -> int:
    # Sort both arrays
    students.sort()
    seats.sort()
    
    moves = 0
    
    # Pair students and seats in sorted order
    for i in range(len(seats)):
        # Add absolute distance between each pair
        moves += abs(seats[i] - students[i])
    
    return moves
```

**Logic:**
1. Sort both arrays
2. Pair smallest with smallest, 2nd smallest with 2nd smallest, etc.
3. Sum all absolute distances
4. Return total moves

---

## LAYER 2: Worked Examples

**Example 1: seats=[3,1,5], students=[2,7,4]**

```
Sort students: [2,4,7]
Sort seats: [1,3,5]

Pairing (sorted order):
  Student 2 → Seat 1: |1-2| = 1 move
  Student 4 → Seat 3: |3-4| = 1 move
  Student 7 → Seat 5: |5-7| = 2 moves

Total: 1 + 1 + 2 = 4 moves ✓
```

**Example 2: seats=[4,1,5,9], students=[1,3,2,6]**

```
Sort students: [1,2,3,6]
Sort seats: [1,4,5,9]

Pairing:
  Student 1 → Seat 1: |1-1| = 0 moves
  Student 2 → Seat 4: |4-2| = 2 moves
  Student 3 → Seat 5: |5-3| = 2 moves
  Student 6 → Seat 9: |9-6| = 3 moves

Total: 0 + 2 + 2 + 3 = 7 moves ✓
```

**Example 3: seats=[2,2,6,6], students=[1,3,2,6]**

```
Sort students: [1,2,3,6]
Sort seats: [2,2,6,6]

Pairing:
  Student 1 → Seat 2: |2-1| = 1 move
  Student 2 → Seat 2: |2-2| = 0 moves
  Student 3 → Seat 6: |6-3| = 3 moves
  Student 6 → Seat 6: |6-6| = 0 moves

Total: 1 + 0 + 3 + 0 = 4 moves ✓
```

**Why sorting is optimal:**

```
Counter-example: What if we DON'T sort?
seats = [3,1,5], students = [2,7,4]

Random pairing:
  Student 2 → Seat 3: |3-2| = 1
  Student 7 → Seat 1: |1-7| = 6  (far!)
  Student 4 → Seat 5: |5-4| = 1
Total: 1 + 6 + 1 = 8 moves

With sorting: 4 moves ✓ (Better!)

Why? Sorting matches extremes:
- Leftmost students pair with leftmost seats
- Rightmost students pair with rightmost seats
- No crossing/criss-crossing paths
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(n log n) - sorting dominates |
| **Space** | O(1) - only storing moves |
| **Strategy** | Sort both, pair in order |
| **Why it works** | Matching sorted order minimizes crossings |

**Mathematical principle:**
- To minimize sum of distances, pair sorted arrays in same order
- No "crossing" assignments (which would waste distance)
- This is optimal assignment problem solution

---

## LAYER 4: Interview Variations

• **If asked "assign to maximize moves"** → Pair opposite order (smallest with largest)  
• **If asked "number of students per seat"** → Use hash map  
• **If asked "which student goes to which seat"** → Track indices  
• **If asked "verify solution"** → Check each student is assigned exactly once  

---

## LAYER 5: Cheat Sheet

```python
def minMovesToSeat(self, seats, students):
    students.sort()
    seats.sort()
    return sum(abs(seats[i] - students[i]) for i in range(len(seats)))
```

**Why sorting works:**
- Sorted order = optimal assignment
- Each student gets closest available seat
- No beneficial "crossing" assignments exist

---

**Time:** O(n log n) | **Space:** O(1)
