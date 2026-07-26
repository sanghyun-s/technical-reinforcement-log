# 🧩 Pattern Cheat Sheet

**Updated:** May 15, 2026 | Week 1 Complete (11/11 problems)

---

## 1️⃣ Hash Map / Dictionary

**When to use:**
- Fast lookup needed (O(1))
- Store value-to-index mapping
- Count frequencies

**Template:**
```python
def twoSum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []
```

**Time:** O(n) | **Space:** O(n)  
**Problems:** #1

---

## 2️⃣ Two Pointers

**When to use:**
- Sorted array
- Find pairs/triplets
- Remove elements in-place
- Container/area problems
- Opposite ends approach

**Template (Partition):**
```python
def twoPointers(arr):
    left, right = 0, len(arr) - 1
    while left < right:
        if condition(arr[left], arr[right]):
            left += 1
        else:
            right -= 1
    return result
```

**Time:** O(n) | **Space:** O(1)  
**Problems:** #26, #27, #35, #11, #15, #88

---

## 3️⃣ Binary Search

**When to use:**
- Sorted array
- Need O(log n)
- Find insertion position
- Find first/last occurrence

**Template:**
```python
def binarySearch(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = (left + right) // 2
        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return left  # insertion position
```

**Time:** O(log n) | **Space:** O(1)  
**Problems:** #35

---

## 4️⃣ Backtracking

**When to use:**
- Generate combinations/permutations
- Explore all paths
- Need to undo choices

**Template (Combinations - with reuse):**
```python
def combinations(candidates, target):
    results = []
    def backtrack(remain, comb, start):
        if remain == 0:
            results.append(list(comb))
            return
        for i in range(start, len(candidates)):
            comb.append(candidates[i])
            backtrack(remain - candidates[i], comb, i)  # i allows reuse
            comb.pop()
    backtrack(target, [], 0)
    return results
```

**Template (Permutations - no reuse):**
```python
def permutations(nums):
    results = []
    def backtrack(curr):
        if len(curr) == len(nums):
            results.append(curr[:])
            return
        for num in nums:
            if num not in curr:
                curr.append(num)
                backtrack(curr)
                curr.pop()
    backtrack([])
    return results
```

**Time:** O(n! × n) for permutations | **Space:** O(n)  
**Problems:** #39, #46

---

## 5️⃣ String/Array Manipulation

**When to use:**
- Prefix/suffix operations
- Array modification in-place
- String pattern matching
- Character-by-character processing

**Key Techniques:**
- Common prefix → compare character-by-character
- Plus one → carry from right to left
- Merge sorted → two pointers from ends

**Problems:** #14, #66, #88

---

## 6️⃣ Greedy Two-Pointer

**When to use:**
- Maximize/minimize area/distance
- Width decreases, optimize height
- Move pointer to smaller value

**Template:**
```python
def maxArea(height):
    left, right = 0, len(height) - 1
    maxarea = 0
    while left < right:
        area = (right - left) * min(height[left], height[right])
        maxarea = max(maxarea, area)
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
    return maxarea
```

**Time:** O(n) | **Space:** O(1)  
**Problems:** #11

---

## Pattern Mastery Map

```
Week 1 (Complete ✅):
├─ Hash Maps: 1 problem
├─ Two Pointers: 5 problems
├─ Binary Search: 1 problem
├─ Backtracking: 2 problems
├─ Array/String: 2 problems
└─ Greedy: 1 problem
   Total: 11 problems ✅

Week 2 (Upcoming):
├─ Sliding Window
├─ Stack & Queues
└─ Linked Lists
```

---

## Key Takeaways

✅ **Hash Maps** → O(1) lookups, complement technique  
✅ **Two Pointers** → Sorted arrays, pairs/triplets  
✅ **Binary Search** → O(log n), insertion point natural  
✅ **Backtracking** → All combinations/permutations, reuse control  
✅ **Greedy** → Optimize at each step with constraints  

---

## Quick Reference by Problem #

| # | Pattern | Template |
|---|---------|----------|
| 1 | Hash Map | seen[complement] lookup |
| 11 | Greedy TP | left/right, move shorter |
| 14 | String | shrink prefix, .find() == 0 |
| 15 | TP + Sort | fix one, TP on rest |
| 26 | TP Dedup | write only on change |
| 27 | TP Filter | write on non-target |
| 35 | Binary Search | left = insertion point |
| 39 | Backtrack+Reuse | pass i not i+1 |
| 46 | Backtrack No-Reuse | check num not in curr |
| 66 | Carry Logic | right to left, all-9s case |
| 88 | Copy+Sort | simple but O((m+n)log(m+n)) |

---
