# 0011. Container With Most Water

**Difficulty:** Medium  
**Pattern:** Greedy / Two Pointers  
**Date Solved:** 2026-05-15  
**Acceptance Rate:** 60.0%  
**Status:** ✅

---

## Understanding the Goal

Find two lines that form container with maximum water capacity. Capacity = width × minimum height.

**Key insight:** Start at extremes (widest), then move inward. Only move shorter line (might find taller).

**Examples Visualized:**

```
height = [1,8,6,2,5,4,8,3,7]

Start: left=0 (h=1), right=8 (h=7)
  width=8, h=min(1,7)=1, area=8×1=8

Move shorter (left):
  left=1 (h=8), right=8 (h=7)
  width=7, h=min(8,7)=7, area=7×7=49 ✓ (Maximum)
```

---

## LAYER 1: Line-by-Line Explanation

```python
class Solution:
    def maxArea(self, height: List[int]) -> int:
        # Two pointers at extremes
        left = 0
        right = len(height) - 1
        maxarea = 0
        
        # Move inward until pointers meet
        while left < right:
            # Width is distance between pointers
            width = right - left
            
            # Water level determined by shorter line
            h = min(height[left], height[right])
            
            # Area = width × height
            area = width * h
            
            # Track maximum
            maxarea = max(maxarea, area)
            
            # Move pointer pointing to shorter line
            # Taller line won't help (limited by shorter)
            # Only shorter line moving might find taller line
            if height[left] < height[right]:
                left += 1
            else:
                right -= 1
        
        return maxarea
```

---

## LAYER 2: Worked Examples

**Example 1: [1,8,6,2,5,4,8,3,7]**
```
left=0 (h=1), right=8 (h=7)
  width=8, h=1, area=8 → left++ (1 < 7)

left=1 (h=8), right=8 (h=7)
  width=7, h=7, area=49 → right-- (8 > 7)

left=1 (h=8), right=7 (h=3)
  width=6, h=3, area=18 → right-- (8 > 3)

...continue...

Result: 49 ✓
```

**Example 2: [1,1]**
```
left=0 (h=1), right=1 (h=1)
  width=1, h=1, area=1 → right-- (equal, either works)

left=0, right=0: exit

Result: 1 ✓
```

---

## LAYER 3: Key Insights

**Pattern:** Greedy Two-Pointer Moving Inward

| Aspect | Value |
|--------|-------|
| **Time** | O(n) - each pointer moves at most n times |
| **Space** | O(1) - only pointers |
| **Greedy** | Move shorter line (only hope for larger area) |

**Common mistakes:**
- ❌ Moving wrong pointer (taller instead of shorter)
- ❌ Not tracking max area
- ❌ Using nested loops (O(n²))
- ❌ Checking `left <= right` instead of `left < right`

**When to use:**
- Find optimal pair with constraint
- Width/distance decreases inward
- Greedy selection valid

---

## LAYER 4: Interview Variations

• **If question asks "find indices instead of area"** → Return `[left, right]` instead of maxarea value

• **If question says "all pairs with area ≥ k"** → Collect all pairs meeting condition instead of tracking max

• **If question adds "no adjacent containers"** → Skip adjacent elements, adjust loop logic

• **If question asks "minimum area instead"** → Still use two-pointer but move taller line (opposite logic)

---

## LAYER 5: Cheat Sheet Entry

**Pattern:** Greedy Two-Pointer

**Template:**
```python
def maxArea(height):
    left, right = 0, len(height) - 1
    maxarea = 0
    while left < right:
        width = right - left
        h = min(height[left], height[right])
        area = width * h
        maxarea = max(maxarea, area)
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
    return maxarea
```

**Key insight:** Greedy: move shorter pointer for optimization

**Related problems:** #42 (Trapping Rain Water), #84 (Largest Rectangle), #238 (Product Except Self)

---

**Complexity Analysis**
- **Time:** O(n)
- **Space:** O(1)

**What I Learned**
- Greedy approach with two pointers
- Why moving shorter pointer is optimal
- Constraint analysis for algorithm design
