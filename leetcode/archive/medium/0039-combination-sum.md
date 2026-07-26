# 0039. Combination Sum

**Difficulty:** Medium  
**Pattern:** Backtracking  
**Date Solved:** 2026-05-15  
**Acceptance Rate:** 76.5%  
**Status:** ✅

---

## Understanding the Goal

Find all unique combinations that sum to target. Same number can be reused unlimited times.

**Key challenge:** Generate combinations, not permutations. Allow reuse by passing same index.

**Examples Visualized:**

```
candidates = [2,3,6,7], target = 7

Path 1: [2,2,3] = 7 ✓ (2 reused)
Path 2: [7] = 7 ✓

No path 3: [3,3,1] (1 not in candidates)
```

---

## LAYER 1: Line-by-Line Explanation

```python
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        results = []
        
        def backtrack(remain, comb, start):
            # Base case 1: Found valid combination
            if remain == 0:
                # Deep copy (prevents reference issues)
                results.append(list(comb))
                return
            
            # Base case 2: Overshot target, stop
            elif remain < 0:
                return
            
            # Try each candidate starting from 'start'
            # 'start' prevents duplicate combinations
            for i in range(start, len(candidates)):
                # Add current candidate
                comb.append(candidates[i])
                
                # Recurse with SAME i (allows reusing)
                # Decrease remain by current number
                backtrack(remain - candidates[i], comb, i)
                
                # Backtrack: remove to try next
                comb.pop()
        
        backtrack(target, [], 0)
        return results
```

---

## LAYER 2: Worked Examples

**Example 1: candidates=[2,3,6,7], target=7**
```
backtrack(7, [], 0)
  i=0 (cand=2):
    comb=[2]
    backtrack(5, [2], 0)
      i=0 (cand=2):
        comb=[2,2]
        backtrack(3, [2,2], 0)
          i=0: comb=[2,2,2] → backtrack(-1, ...) → return
          i=1 (cand=3):
            comb=[2,2,3]
            backtrack(0, [2,2,3], 1) → append [2,2,3] ✓
      i=1 (cand=3):
        comb=[2,3]
        backtrack(2, [2,3], 1)
          i=1: comb=[2,3,3] → backtrack(-1, ...) → return
  
  i=3 (cand=7):
    comb=[7]
    backtrack(0, [7], 3) → append [7] ✓

Result: [[2,2,3], [7]] ✓
```

**Example 2: candidates=[2,3,5], target=8**
```
backtrack(8, [], 0)
  i=0 (cand=2):
    comb=[2] → backtrack(6, [2], 0)
      → eventually finds [2,2,2,2] ✓
      → and [2,3,3] ✓
  
  i=1 (cand=3):
    comb=[3] → backtrack(5, [3], 1)
      → finds [3,5] ✓
  
  i=2 (cand=5):
    comb=[5] → backtrack(3, [5], 2)
      → no valid (5 > 3)

Result: [[2,2,2,2], [2,3,3], [3,5]] ✓
```

---

## LAYER 3: Key Insights

**Pattern:** Backtracking with Reuse (DFS)

| Aspect | Value |
|--------|-------|
| **Time** | O(N^(T/M)) - exponential |
| **Space** | O(T/M) - recursion depth |
| **Key param** | `start` prevents duplicates, `i` (not `i+1`) allows reuse |

**Common mistakes:**
- ❌ Using `i+1` instead of `i` (prevents reuse)
- ❌ Not deep copying with `list(comb)`
- ❌ Checking `remain < 0` after appending (wasted calls)
- ❌ Forgetting to pop() after recursion

**When to use:**
- Find all combinations with constraints
- Explore multiple paths
- Reuse elements allowed

---

## LAYER 4: Interview Variations

• **If question says "each number used at most once"** → Pass `i+1` instead of `i` in recursion (Combination Sum II)

• **If question adds "exactly k numbers in combo"** → Add counter parameter, check `count==k` alongside `remain==0`

• **If question asks "minimum combinations to reach target"** → Use dynamic programming instead (dp[i] = min count to reach sum i)

• **If question has "combination with duplicates in candidates"** → Sort first, then skip duplicate values in loop

---

## LAYER 5: Cheat Sheet Entry

**Pattern:** Backtracking with Reuse

**Template:**
```python
def combinationSum(candidates, target):
    results = []
    def backtrack(remain, comb, start):
        if remain == 0:
            results.append(list(comb))
            return
        elif remain < 0:
            return
        
        for i in range(start, len(candidates)):
            comb.append(candidates[i])
            backtrack(remain - candidates[i], comb, i)  # i, not i+1
            comb.pop()
    
    backtrack(target, [], 0)
    return results
```

**Key insight:** `start` prevents duplicates, `i` allows reuse

**Related problems:** #40 (Combination Sum II), #77 (Combinations), #216 (Combination Sum III)

---

**Complexity Analysis**
- **Time:** O(N^(T/M))
- **Space:** O(T/M)

**What I Learned**
- Backtracking for combination generation
- Difference between combinations and permutations
- How to enable/disable reuse with pointer management
