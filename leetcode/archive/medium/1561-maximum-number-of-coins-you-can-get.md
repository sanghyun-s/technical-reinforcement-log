# 1561. Maximum Number of Coins You Can Get

**Difficulty:** Medium
**Pattern:** Greedy / Sort + Take Every Other / Adversarial Game
**Date Solved:** 2026-06-14
**Status:** ✅

---

## Understanding the Goal

3n piles, n rounds. Each round: pick any 3 piles → Alice gets biggest, you get middle, Bob gets smallest. Maximize YOUR total.

**The hints walk you straight to the greedy insight:**

**Hint 1:** "Which pile will you never get?" — the absolute largest.

**Hint 2:** "Bob is forced to take the smallest. Which pile should you give to him?" — the **n smallest piles overall**. Sacrifice the bottom n piles to Bob; they're wasted no matter what.

**The optimal strategy (sorted ascending):**

```
Sorted: [p₀, p₁, ..., pₙ₋₁ | pₙ, pₙ₊₁, ..., p₃ₙ₋₁ ]
         ↑————— Bob's ———↑   ↑—— pair (us, Alice) ——↑
```

- **Bob gets the n smallest:** `p[0..n-1]`
- **Pair the remaining 2n piles consecutively:** `(p[n], p[n+1]), (p[n+2], p[n+3]), ..., (p[3n-2], p[3n-1])`. In each pair, **you take the smaller**, Alice the larger.
- **Your take:** `p[n], p[n+2], p[n+4], ..., p[3n-2]` — every other from index n

In sorted ascending: `sorted(piles)[n::2]`.

Same family as **LC 561 Array Partition (Day 8)** — generalized from pairs to triplets.

---

## LAYER 1: Line-by-Line Explanation

### Approach 1: Editorial — sort + deque simulation

```python
from collections import deque

class Solution:
    def maxCoins(self, piles: List[int]) -> int:
        piles.sort()
        queue = deque(piles)
        ans = 0
        while queue:
            queue.pop()              # Right = largest → Alice
            ans += queue.pop()       # Right again = 2nd largest → US
            queue.popleft()          # Left = smallest → Bob
        return ans
```

**Why deque, not list:**
- `list.pop(0)` is O(n) (shifts all elements)
- `deque.popleft()` is O(1)

Using deque keeps both pop operations O(1) → loop is O(n).

### Approach 2: The Pythonic One-Liner ⭐

```python
class Solution:
    def maxCoins(self, piles: List[int]) -> int:
        # Sort ascending → bottom n piles go to Bob (indices 0..n-1)
        # Remaining 2n piles paired → we take every other starting at index n
        # [n::2]: indices n, n+2, n+4, ..., 3n-2 — exactly n elements
        return sum(sorted(piles)[len(piles) // 3::2])
```

For `[2,4,1,2,7,8]`, sorted = `[1,2,2,4,7,8]`, n=2:
- `[2::2]` → indices 2, 4 → values `[2, 7]` → sum = 9 ✓

### Approach 3: Sort + Index Loop

```python
class Solution:
    def maxCoins(self, piles: List[int]) -> int:
        piles.sort()
        n = len(piles) // 3
        return sum(piles[i] for i in range(n, len(piles), 2))
```

### Approach 4: Heap-based (overkill but instructive)

```python
import heapq

class Solution:
    def maxCoins(self, piles: List[int]) -> int:
        heap = [-p for p in piles]
        heapq.heapify(heap)
        n = len(piles) // 3
        total = 0
        for _ in range(n):
            heapq.heappop(heap)                # Alice (discard)
            total += -heapq.heappop(heap)      # us
        return total
```

Heap separates "top 2n" from "bottom n" without explicit deque handling.

---

## LAYER 2: Worked Examples

### Example 1: `piles = [2, 4, 1, 2, 7, 8]` → 9

Sorted: `[1, 2, 2, 4, 7, 8]`, n=2.
- Bob: `1, 2` (bottom 2)
- Pairs: `(2, 4), (7, 8)` — we get smaller of each
- Our piles: `2 + 7 = 9` ✓

Slice: `sorted[2::2]` = `[2, 7]` → sum = **9** ✓

### Example 3: `piles = [9, 8, 7, 6, 5, 1, 2, 3, 4]` → 18

Sorted: `[1, 2, 3, 4, 5, 6, 7, 8, 9]`, n=3.
- Bob: `1, 2, 3`
- Pairs: `(4, 5), (6, 7), (8, 9)` — we get `4, 6, 8`
- Total: **18** ✓

### Sanity check — why consecutive pairing is optimal

For `[4, 5, 6, 7]` (after Bob's share removed):

| Pairing | Our piles | Sum |
|---------|-----------|-----|
| (4,5), (6,7) | 4, 6 | **10** ← consecutive |
| (4,6), (5,7) | 4, 5 | 9 |
| (4,7), (5,6) | 4, 5 | 9 |

**Consecutive pairing wins.** Pairing 6 with 7 forces Alice to take 7, leaving us with 6.

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **Sort + slice** ⭐ | **O(n log n)** | **O(1)** | **One line; preferred** |
| Sort + deque | O(n log n) | O(n) | Editorial |
| Heap | O(n log n) | O(n) | Same complexity, different mechanic |

**The "sort and take every other" greedy pattern (MATURE now):**

You've seen this 4 times:
- **LC 561 (Day 8) Array Partition I:** pair sorted, sum mins → `sum(sorted(nums)[::2])`
- **LC 1874 (Day 5) Min Product Sum:** pair opposites
- **LC 2037 (Day 5) Seat Everyone:** sort + diff
- **LC 1561 (this):** triplet variant — drop n smallest, then take every other

General schema:
> "Items grouped; in each group an adversary takes the worst position. Sort. Sacrifice the worst items to adversary. Pair/triple the rest greedily."

**Why this is greedy, not DP (exchange argument):**

Suppose optimal doesn't pair consecutive sorted piles. Then ∃ pairs (a, b), (c, d) with a < c < b < d. Show swapping to (a, c), (b, d) gives at least as good — by case analysis, consecutive pairing is always ≥ any other.

**Why Bob is forced bottom n:**

If Bob got anything but bottom n, swap improves our/Alice's sum. Iterate → Bob's bottom n is optimal.

**Connection to LC 561:**

| LC 561 | LC 1561 |
|--------|---------|
| 2n elements in pairs | 3n elements in triplets |
| Take min of each pair | Take middle of each triplet |
| `sum(sorted(nums)[::2])` | `sum(sorted(piles)[n::2])` |

**LC 1561 = LC 561 + "sacrifice the bottom n"**. Same structural idea, additional layer.

**Slice arithmetic:** `arr[n::2]` from index n, every other to end. For 3n elements starting at n, this yields exactly n values: indices n, n+2, ..., 3n-2.

**`deque` over `list` for two-ended operations:** `pop(0)` is O(n) on list; `popleft()` is O(1) on deque. Memorize.

---

## LAYER 4: Interview Variations

• **You pick first:** Trivial — sum of top-n.
• **k people per round:** Sacrifice bottom (k-2)*n; take every (k-1)-th.
• **Group sizes variable:** Combinatorial; harder.
• **Bob maximizes:** sum of bottom n.
• **Refuse triplets allowed:** Game-theoretic.
• **2D piles:** Apply per-row, sum.
• **Online (piles arrive):** Heap; maintain top 2n/3.
• **Find which triplets:** Output indices.
• **k-th smallest:** Different problem.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — one-liner ⭐
class Solution:
    def maxCoins(self, piles: List[int]) -> int:
        return sum(sorted(piles)[len(piles) // 3::2])
```

**Editorial — sort + deque:**
```python
from collections import deque

def maxCoins(piles):
    piles.sort()
    queue = deque(piles)
    ans = 0
    while queue:
        queue.pop()              # Alice
        ans += queue.pop()       # us
        queue.popleft()          # Bob
    return ans
```

**Explicit index loop:**
```python
def maxCoins(piles):
    piles.sort()
    n = len(piles) // 3
    return sum(piles[i] for i in range(n, len(piles), 2))
```

**Heap-based:**
```python
import heapq

def maxCoins(piles):
    heap = [-p for p in piles]
    heapq.heapify(heap)
    n = len(piles) // 3
    total = 0
    for _ in range(n):
        heapq.heappop(heap)               # Alice
        total += -heapq.heappop(heap)     # us
    return total
```

**Two-pointer compact:**
```python
def maxCoins(piles):
    piles.sort()
    left, right = 0, len(piles) - 1
    ans = 0
    while left < right:
        right -= 1                # Alice took piles[right]
        ans += piles[right]       # we take piles[right-1] (new right)
        right -= 1
        left += 1                 # Bob took piles[left]
    return ans
```

---

**Time:** O(n log n) | **Space:** O(1) in-place or O(n) for deque

**Pattern flag:** "Group items; adversary takes worst position in each group" → **sort + sacrifice absolute worst items to wasted slots + greedy pair/group rest**.

For this triplet game: **sort ascending → bottom n are Bob's → remaining 2n paired consecutively → you get every other starting at index n**. One slice: `sum(sorted(piles)[n::2])`.

The **exchange argument** is the standard proof of greedy correctness — show any non-greedy choice can be swapped to greedy without loss.

**Mature "sort and take every other" family** — LC 561 (Day 8) was the pairs version, LC 1561 (this) is the triplets version. Same idea, generalized.
