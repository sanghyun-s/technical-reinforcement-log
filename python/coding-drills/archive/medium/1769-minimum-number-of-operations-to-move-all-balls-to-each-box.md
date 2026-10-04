# 1769. Minimum Number of Operations to Move All Balls to Each Box

**Difficulty:** Medium
**Pattern:** String / Prefix Sum / Two-Pass Directional Sweep
**Date Solved:** 2026-06-05
**Status:** ✅

---

## Understanding the Goal

For each position `i`, compute total cost to gather every ball to box `i`. Moving ball from `j` to `i` costs `|i − j|`. So:

```
answer[i] = sum of |i − j| over every j where boxes[j] == '1'
```

Editorial: O(n²) brute force. Works for n ≤ 2000.

**Key insight (O(n) upgrade):** Same skeleton as yesterday's LC 2574. Decompose cost into:
- Cost from balls on the **LEFT** of i (each contributes `i − j` where j < i)
- Cost from balls on the **RIGHT** of i (each contributes `j − i` where j > i)

Both computable with a **forward pass + backward pass**, maintaining running ball count and running cost. Same "store-before-update" pattern.

---

## LAYER 1: Line-by-Line Explanation

**Editorial — O(n²) brute force:**

```python
class Solution:
    def minOperations(self, boxes: str) -> List[int]:
        answer = [0] * len(boxes)                       # Output array, all zeros
        
        for current_box in range(len(boxes)):           # Iterate every position
            # Only positions with balls contribute to anyone's cost
            if boxes[current_box] == "1":
                # For each target, this ball contributes |distance|
                for new_position in range(len(boxes)):
                    answer[new_position] += abs(new_position - current_box)
        
        return answer
```

For n=2000: up to 4M ops. Acceptable but wasteful.

**The O(n) two-pass sweep (interview-grade):**

```python
class Solution:
    def minOperations(self, boxes: str) -> List[int]:
        n = len(boxes)
        answer = [0] * n
        
        # ---------- LEFT-TO-RIGHT PASS ----------
        # At position i: accumulate cost from balls to the LEFT
        balls = 0                                       # Count of balls seen so far (left side)
        cost = 0                                        # Total cost to bring left balls to i
        
        for i in range(n):
            answer[i] += cost                           # Add left-side cost
            balls += int(boxes[i])                      # If this position has a ball, count it
            cost += balls                               # Each step right: every left ball +1 distance
        
        # ---------- RIGHT-TO-LEFT PASS ----------
        balls = 0
        cost = 0
        
        for i in range(n - 1, -1, -1):                  # Walk right-to-left
            answer[i] += cost                           # Add right-side cost
            balls += int(boxes[i])
            cost += balls
        
        return answer
```

**Why `cost += balls` works:**

When you move cursor from `i` to `i+1`:
- Every ball at or left of `i` is now 1 step further from `i+1`
- So total cost from left increases by exactly `balls` (the count of left balls)

This is the crux — incremental update vs recomputing distances.

**Why `answer[i] += cost` BEFORE counting this position's ball:**

Same "store-before-update" pattern from LC 2574:
- `cost` represents balls STRICTLY to the left (positions 0..i-1)
- Add to answer first
- THEN update `balls` and `cost` to include position `i` (for next iteration)

---

## LAYER 2: Worked Examples

**Example 1: `boxes = "110"` → `[1, 1, 3]`**

Balls at positions 0 and 1.

Brute-force trace:

| target i | distances | answer[i] |
|----------|-----------|-----------|
| 0 | \|0-0\| + \|0-1\| = 0 + 1 | 1 |
| 1 | \|1-0\| + \|1-1\| = 1 + 0 | 1 |
| 2 | \|2-0\| + \|2-1\| = 2 + 1 | 3 |

Result: `[1, 1, 3]` ✓

**O(n) trace:**

LR pass:

| i | balls before | cost before | answer[i] += cost | balls after | cost after |
|---|--------------|-------------|---------------------|-------------|------------|
| 0 | 0 | 0 | 0 | 1 | 1 |
| 1 | 1 | 1 | 1 | 2 | 3 |
| 2 | 2 | 3 | 3 | 2 | 5 |

After LR: `[0, 1, 3]`

RL pass (reset):

| i | balls before | cost before | answer[i] += cost | balls after | cost after |
|---|--------------|-------------|---------------------|-------------|------------|
| 2 | 0 | 0 | 3 + 0 = 3 | 0 | 0 |
| 1 | 0 | 0 | 1 + 0 = 1 | 1 | 1 |
| 0 | 1 | 1 | 0 + 1 = 1 | 2 | 3 |

Final: `[1, 1, 3]` ✓

**Example 2: `boxes = "001011"` → `[11, 8, 5, 4, 3, 4]`**

Balls at positions 2, 4, 5.

Verifying answer[0]: 2 + 4 + 5 = 11 ✓
Verifying answer[3]: 1 + 1 + 2 = 4 ✓

**Intuition for "cost += balls":**

Each existing ball is "stretched" by 1 step as cursor moves. New balls join the chain at distance 0 but contribute to future increments.

**Edge cases:**
- Single box "0": `[0]`
- Single box "1": `[0]` (ball already there)
- All zeros: all zeros
- All ones: symmetric U-shape; middle is cheapest
- One ball at position p: answer[i] = |p − i|

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Brute force (editorial) | O(n²) | O(n) | Works for n=2000 |
| **Two-pass sweep** | **O(n)** | **O(n)** | **Interview-grade** |

**Why O(n) matters:**

The pattern of decomposing "sum over all pairs" into directional running sums is one of the highest-yield array tricks. Appears in:
- LC 2574 (Left/Right Sum Differences) — yesterday
- LC 1480 (Running Sum) — yesterday
- LC 238 (Product Except Self)
- LC 42 (Trapping Rain Water) — left-max + right-max sweeps
- Histogram and area problems

If you see "for each i, compute something involving all other j's," reach for two-pass sweep.

**Why "cost += balls" is the magic:**

Linearity of distance: total cost across multiple sources changes linearly with cursor position, with slope = count of sources. Like a rubber band stretching.

**Connection to LC 2574:**

LC 2574 asked for `|leftSum - rightSum|`. This problem asks for `leftCost + rightCost`. Same skeleton, different aggregation.

**The store-before-update pattern again:**

```python
for i in range(n):
    answer[i] += running_state            # USE current state (excludes i)
    update_running_state_to_include(i)    # THEN update
```

Almost every prefix-based array problem uses this. Memorize.

---

## LAYER 4: Interview Variations

• **Min cost to single box (median trick):** Median of ball positions minimizes total distance. O(n).
• **Quadratic distance ((i-j)²):** Decompose via running `sum(j)` and `sum(j²)` separately.
• **Weighted balls:** Replace ball count with sum of weights.
• **2D grid balls:** Decompose into row and column problems (Manhattan is separable).
• **Skip some balls — choose k of them:** DP or greedy.
• **Nonlinear cost per step:** Restructure; running-update may not work.
• **Largest answer:** Take max.
• **Best gathering point (min cost):** argmin.
• **Multiple ball types:** Sum independent results.
• **Streaming:** Maintain prefix counts; query O(1), updates O(n).
• **Online with move op:** Fenwick tree (BIT) for range sums.
• **Capped cost (`min(|i-j|, k)`):** Segmented logic.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Two-pass O(n) sweep
class Solution:
    def minOperations(self, boxes: str) -> List[int]:
        n = len(boxes)
        answer = [0] * n
        
        # Left-to-right: cost from LEFT balls
        balls = cost = 0
        for i in range(n):
            answer[i] += cost
            balls += int(boxes[i])
            cost += balls
        
        # Right-to-left: cost from RIGHT balls
        balls = cost = 0
        for i in range(n - 1, -1, -1):
            answer[i] += cost
            balls += int(boxes[i])
            cost += balls
        
        return answer
```

**Editorial — O(n²) brute force:**
```python
def minOperations(boxes):
    answer = [0] * len(boxes)
    for j in range(len(boxes)):
        if boxes[j] == "1":
            for i in range(len(boxes)):
                answer[i] += abs(i - j)
    return answer
```

**One-liner brute force:**
```python
def minOperations(boxes):
    return [sum(abs(i - j) for j, b in enumerate(boxes) if b == '1')
            for i in range(len(boxes))]
```

**With explicit left/right arrays (debug-friendly):**
```python
def minOperations(boxes):
    n = len(boxes)
    left_cost = [0] * n
    right_cost = [0] * n
    
    balls = cost = 0
    for i in range(n):
        left_cost[i] = cost
        balls += int(boxes[i])
        cost += balls
    
    balls = cost = 0
    for i in range(n - 1, -1, -1):
        right_cost[i] = cost
        balls += int(boxes[i])
        cost += balls
    
    return [l + r for l, r in zip(left_cost, right_cost)]
```

---

**Time:** O(n) | **Space:** O(n) for output

**Pattern flag:** "For each i, compute sum of |i − j| over all j" → **two-pass directional sweep**. Trick: total cost from one side updates as `cost += count_so_far` per step. The most distilled form of prefix-sweep — same skeleton powers trapping rain water, product except self, and many more. Store-before-update kernel: `answer[i] += state; THEN update_state_with(i)`.
