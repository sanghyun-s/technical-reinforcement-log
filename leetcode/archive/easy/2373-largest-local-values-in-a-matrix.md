# 2373. Largest Local Values in a Matrix

**Difficulty:** Easy  
**Pattern:** Matrix / Sliding Window / 2D Iteration  
**Date Solved:** 2026-05-16  
**Status:** ✅

---

## Understanding the Goal

For each possible 3×3 window in the grid, find the maximum value and place it in the result matrix.

**Key insight:** Iterate through all valid top-left corners of 3×3 windows, find max in each, store in result.

---

## LAYER 1: Editorial Code Explained

```python
def largestLocal(self, grid):
    N = len(grid)
    
    # Create result matrix of size (N-2) × (N-2)
    # Because there are (N-2)×(N-2) possible 3×3 windows
    max_local = [[0] * (N - 2) for _ in range(N - 2)]
    
    # Iterate through each possible 3×3 window top-left corner
    for i in range(N - 2):
        for j in range(N - 2):
            # Find max in 3×3 window starting at (i, j)
            max_local[i][j] = self.find_max(grid, i, j)
    
    return max_local

def find_max(self, grid, x, y):
    # Find maximum in 3×3 window with top-left at (x, y)
    max_element = 0
    
    # Iterate through 3×3 window
    for i in range(x, x + 3):
        for j in range(y, y + 3):
            max_element = max(max_element, grid[i][j])
    
    return max_element
```

**Logic:**
1. Create result matrix of size (N-2) × (N-2)
2. For each valid top-left corner (i, j):
   - Iterate through 3×3 window starting at (i, j)
   - Find maximum value in that window
   - Store in result[i][j]

---

## LAYER 2: Worked Example

**Example 1: [[9,9,8,1],[5,6,2,6],[8,2,6,4],[6,2,2,2]]**

```
Grid (4×4):
9  9  8  1
5  6  2  6
8  2  6  4
6  2  2  2

Possible 3×3 windows: (4-2)×(4-2) = 2×2

Window [0][0] (top-left at 0,0):
9  9  8
5  6  2
8  2  6
max = 9 → result[0][0] = 9

Window [0][1] (top-left at 0,1):
9  8  1
6  2  6
2  6  4
max = 9 → result[0][1] = 9

Window [1][0] (top-left at 1,0):
5  6  2
8  2  6
6  2  2
max = 8 → result[1][0] = 8

Window [1][1] (top-left at 1,1):
6  2  6
2  6  4
2  2  2
max = 6 → result[1][1] = 6

Result: [[9,9], [8,6]] ✓
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O((n-2)² × 9) = O(n²) |
| **Space** | O((n-2)²) for result matrix |
| **Windows** | (n-2) × (n-2) possible 3×3 windows |
| **Window iteration** | Fixed 3×3 each time |

**Why (N-2) × (N-2)?**
- For an N×N grid, top-left corner can be at positions [0,0] to [N-3,N-3]
- That's (N-2) positions in each dimension

---

## LAYER 4: Interview Variations

• **If asked "4×4 windows instead"** → Change range(x, x+3) to range(x, x+4)  
• **If asked "minimum instead"** → Use min() instead of max()  
• **If asked "optimize space"** → Build result in-place (complex)  
• **If asked "without helper function"** → Inline the find_max logic  

---

## LAYER 5: Cheat Sheet

**Editorial approach:**
```python
def largestLocal(self, grid):
    N = len(grid)
    max_local = [[0] * (N - 2) for _ in range(N - 2)]
    
    for i in range(N - 2):
        for j in range(N - 2):
            max_local[i][j] = self.find_max(grid, i, j)
    
    return max_local

def find_max(self, grid, x, y):
    max_element = 0
    for i in range(x, x + 3):
        for j in range(y, y + 3):
            max_element = max(max_element, grid[i][j])
    return max_element
```

**One-liner approach:**
```python
def largestLocal(self, grid):
    N = len(grid)
    return [[max(grid[i+di][j+dj] for di in range(3) for dj in range(3)) 
             for j in range(N-2)] for i in range(N-2)]
```

**Using max() with unpacking:**
```python
def largestLocal(self, grid):
    N = len(grid)
    result = []
    for i in range(N - 2):
        row = []
        for j in range(N - 2):
            values = [grid[i+di][j+dj] for di in range(3) for dj in range(3)]
            row.append(max(values))
        result.append(row)
    return result
```

---

**Time:** O(n²) | **Space:** O((n-2)²)
