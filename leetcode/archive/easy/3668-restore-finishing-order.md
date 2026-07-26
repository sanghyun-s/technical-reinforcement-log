# 3668. Restore Finishing Order

**Difficulty:** Easy  
**Pattern:** Hash Set / Filter Preserving Order  
**Date Solved:** 2026-05-19  
**Status:** ✅

---

## Understanding the Goal

You have a race finishing order, and a sorted list of your friends. Return your friends in the order they actually finished — not in the sorted order they were given to you.

**Key insight:** `order` already encodes the answer's sequence. You just need to filter it down to only the friends. Convert `friends` to a set for O(1) membership lookup, then walk `order` once and keep only the friends.

The sorted property of `friends` is a red herring for the algorithm — it's not used.

---

## LAYER 1: Line-by-Line Explanation

```python
from typing import List

class Solution:
    def recoverOrder(self, order: List[int], friends: List[int]) -> List[int]:
        friend_set = set(friends)                       # O(1) lookups instead of O(k)
        return [p for p in order if p in friend_set]    # Walk order, keep only friends
```

**Why a set:** `if p in [1, 3, 4]` is O(k) per check (k = number of friends), making the whole solution O(n*k). `if p in {1, 3, 4}` is O(1), making it O(n + k).

**Why iterate `order`, not `friends`:** Iterating `order` automatically preserves finishing order — which is what the problem asks for. If you iterated `friends`, you'd have to look up each friend's index in `order` and sort by it (more code, same result).

---

## LAYER 2: Worked Examples

**Example 1: `order = [3,1,2,5,4]`, `friends = [1,3,4]` → `[3,1,4]`**

Build set: `friend_set = {1, 3, 4}`

Walk `order`:

| p | in friend_set? | result so far |
|---|----------------|---------------|
| 3 | ✅              | [3]           |
| 1 | ✅              | [3, 1]        |
| 2 | ❌              | [3, 1]        |
| 5 | ❌              | [3, 1]        |
| 4 | ✅              | [3, 1, 4]     |

Output: `[3, 1, 4]` ✓

**Example 2: `order = [1,4,5,3,2]`, `friends = [2,5]` → `[5,2]`**

Set: `{2, 5}`

| p | in set? | result    |
|---|---------|-----------|
| 1 | ❌       | []        |
| 4 | ❌       | []        |
| 5 | ✅       | [5]       |
| 3 | ❌       | [5]       |
| 2 | ✅       | [5, 2]    |

Output: `[5, 2]` ✓

**Edge case: All participants are friends.** `order = [3,1,2]`, `friends = [1,2,3]` → returns `[3,1,2]` (the entire order, unchanged).

**Edge case: Single friend.** `order = [5,2,4,1,3]`, `friends = [4]` → returns `[4]`.

---

## LAYER 3: Key Insights

| Aspect | Value |
|--------|-------|
| **Time** | O(n + k) — build set is O(k), filter is O(n) |
| **Space** | O(k) — for the set; output not counted |
| **Approach** | Set membership + order-preserving filter |

**Why `friends` being sorted is irrelevant:** The problem hands you a sorted list because it's a convenient input format, not because the algorithm exploits it. A set strips the order anyway — we only care "is this person a friend?", not "what's their rank among friends?".

**The general pattern:** "Given source order A and target subset B, return B in A's order." Always: convert B to set, filter A. This shows up constantly in interview problems.

**Why not sort `friends` by their index in `order`?** It works but is uglier:
```python
return sorted(friends, key=lambda f: order.index(f))   # O(n*k), worse
```
`.index()` is O(n) per call → O(n*k) total. Plus it requires `friends` to actually contain the answer, which obscures the intent.

---

## LAYER 4: Interview Variations

• **`friends` may contain IDs NOT in the race:** Same code works — non-existent IDs simply never match during the filter. No special handling needed.

• **Return ranks (1-indexed) instead of IDs:** Track index while iterating:
  ```python
  return [i + 1 for i, p in enumerate(order) if p in friend_set]
  ```

• **Return (id, rank) tuples:**
  ```python
  return [(p, i + 1) for i, p in enumerate(order) if p in friend_set]
  ```

• **Return friends who finished in top K:**
  ```python
  return [p for p in order[:k] if p in friend_set]
  ```

• **Multiple friend groups, return each group's ordering:**
  ```python
  return {name: [p for p in order if p in set(group)] for name, group in groups.items()}
  ```

• **`order` is a stream (can't be re-read), `friends` is small:** Same single pass works. The streaming nature is already accommodated since we only iterate `order` once.

• **`friends` is huge (millions) and `order` is short:** Still iterate `order` — it's the smaller of the two. The pattern naturally scales to whichever is smaller.

• **Return finishing order of friends with their gap to the winner:**
  ```python
  return [(p, i) for i, p in enumerate(order) if p in friend_set]
  # i is the gap (0 = winner, 1 = one place behind, etc.)
  ```

• **What if `order` had duplicates?** (Hypothetical — constraint says no.) The set-based filter would emit a friend's ID every time it appears. You'd need a `seen` set to dedupe.

---

## LAYER 5: Cheat Sheet

```python
# One-liner (preferred)
def recoverOrder(order, friends):
    friend_set = set(friends)
    return [p for p in order if p in friend_set]
```

**Alternative — using filter:**
```python
def recoverOrder(order, friends):
    friend_set = set(friends)
    return list(filter(lambda p: p in friend_set, order))
```

**Alternative — sort by index (works but slower):**
```python
def recoverOrder(order, friends):
    pos = {p: i for i, p in enumerate(order)}
    return sorted(friends, key=pos.get)
```
This is O(n + k log k) instead of O(n + k), but useful template when `friends` doesn't need to come from a contiguous source.

---

**Time:** O(n + k) | **Space:** O(k)

**Pattern flag:** "Filter a sequence by membership in another collection" → always `set(other)` + list comprehension. Drilling this reflex saves time on dozens of LeetCode problems.
