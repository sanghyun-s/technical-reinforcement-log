# 885. Spiral Matrix III

**Difficulty:** Medium  
**Pattern:** Matrix / Simulation / Spiral Traversal  
**Date Solved:** 2026-05-16  
**Status:** ✅

---

## Understanding the Goal

Start at (rStart, cStart) and traverse in a clockwise spiral pattern, visiting all grid cells. Move outside grid boundary when needed, but only record cells inside the grid.

**Key insight:** Use direction array to simulate movement. Increase step size every 2 direction changes.

---

## LAYER 1: Editorial Code Explained

```python
def spiralMatrixIII(self, rows: int, cols: int, rStart: int, cStart: int):
    # Direction vectors: East, South, West, North (clockwise)
    dir = [[0, 1], [1, 0], [0, -1], [-1, 0]]
    traversed = []
    
    # step = 1: move 1 East, 1 South
    # step = 2: move 2 West, 2 North
    # step = 3: move 3 East, 3 South, etc.
    step = 1
    direction = 0
    
    while len(traversed) < rows * cols:
        # Change direction twice before increasing step
        for _ in range(2):
            # Move 'step' times in current direction
            for _ in range(step):
                # Only record if inside grid
                if 0 <= rStart < rows and 0 <= cStart < cols:
                    traversed.append([rStart, cStart])
                
                # Move to next position (may go outside grid)
                rStart += dir[direction][0]
                cStart += dir[direction][1]
            
            # Change direction (East→South→West→North→East...)
            direction = (direction + 1) % 4
        
        # After 2 direction changes, increase step size
        step += 1
    
    return traversed
```

**Logic:**
1. Use direction vectors for clockwise spiral: [0,1], [1,0], [0,-1], [-1,0]
2. Start with step=1: move 1 East, 1 South, then step=2: move 2 West, 2 North
3. For each step and direction, move that many times
4. Only record positions inside grid boundaries
5. Continue until all cells visited

---

## LAYER 2: Worked Example

**Example 1: rows=1, cols=4, start=(0,0)**

```
Step 1, Direction 0 (East):
  (0,0) ✓ inside → add
  Move to (0,1)
  (0,1) ✓ inside → add
  Move to (0,2)
  
Step 1, Direction 1 (South):
  (0,2) ✓ inside → add
  Move to (1,2)
  (1,2) ✗ outside → skip
  Move to (2,2)
  
Step 2, Direction 2 (West):
  (2,2) ✗ outside → skip
  Move to (2,1)
  ...

Result: [[0,0], [0,1], [0,2], [0,3]] ✓
```

**Example 2: rows=5, cols=6, start=(1,4)**

```
Spiral starting at (1,4):
East 1:  (1,4)
South 1: (2,4)
West 2:  (2,3), (1,3)
North 2: (0,3), (0,4)
East 3:  (0,5), (1,5), (2,5)
South 3: (3,5), (4,5), (5,5)-outside
West 4:  (4,4), (4,3), (4,2), (4,1)
North 4: (3,1), (2,1), (1,1), (0,1)
...

(Returns only cells inside 5×6 grid)
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(rows × cols) - visit each cell once |
| **Space** | O(rows × cols) - result array |
| **Step pattern** | 1, 1, 2, 2, 3, 3, 4, 4, ... |
| **Direction cycle** | 0→1→2→3→0 (East→South→West→North) |
| **Key insight** | Increase step after every 2 direction changes |

**Why step increases every 2 directions?**
```
Spiral pattern needs:
- Move 1 East, then 1 South (step 1, 2 directions)
- Move 2 West, then 2 North (step 2, 2 directions)
- Move 3 East, then 3 South (step 3, 2 directions)
Pattern: 1,1,2,2,3,3,4,4...
```

---

## LAYER 4: Interview Variations

• **If asked "counter-clockwise"** → Reverse direction order: [0,-1], [-1,0], [0,1], [1,0]  
• **If asked "don't go outside grid"** → Remove boundary check  
• **If asked "return in-place"** → Modify matrix instead of returning list  
• **If asked "zigzag instead"** → Different step size pattern  

---

## LAYER 5: Cheat Sheet

```python
def spiralMatrixIII(self, rows, cols, rStart, cStart):
    # Directions: East, South, West, North (clockwise)
    directions = [[0, 1], [1, 0], [0, -1], [-1, 0]]
    result = []
    step = 1
    direction = 0
    
    while len(result) < rows * cols:
        for _ in range(2):  # Two directions per step size
            for _ in range(step):
                if 0 <= rStart < rows and 0 <= cStart < cols:
                    result.append([rStart, cStart])
                rStart += directions[direction][0]
                cStart += directions[direction][1]
            direction = (direction + 1) % 4
        step += 1
    
    return result
```

**Key:** Step increases every 2 direction changes (after East+South, then West+North, etc).

---

**Time:** O(rows × cols) | **Space:** O(rows × cols)
