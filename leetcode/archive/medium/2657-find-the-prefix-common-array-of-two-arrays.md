# 2657. Find the Prefix Common Array of Two Arrays

**Difficulty:** Medium
**Pattern:** Hash Set / Bit Manipulation / Permutation Trick
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

For each index `i`, count how many values appear in **both** `A[0..i]` and `B[0..i]`. Return the running count.

**Key insight (the permutation trick):** Since `A` and `B` are both permutations of `1..n`, every value appears exactly once in each. As we extend both prefixes:
- A value enters the "common set" the moment it has been seen in both arrays
- Each value contributes at most +1 to the running count

This unlocks O(n): maintain `freq[v]` = times value v seen across BOTH arrays. When `freq[v]` hits 2, that value just became common.

The editorial gives O(n³) triple-nested loops. The frequency-counter trick is the Medium-level insight.

---

## LAYER 1: Line-by-Line Explanation

**Editorial — Brute force O(n³):**

```python
class Solution:
    def findThePrefixCommonArray(self, A: list, B: list) -> list:
        n = len(A)
        prefix_common_array = [0] * n
        for current_index in range(n):
            common_count = 0
            for a_index in range(current_index + 1):
                for b_index in range(current_index + 1):
                    if A[a_index] == B[b_index]:
                        common_count += 1
                        break
            prefix_common_array[current_index] = common_count
        return prefix_common_array
```

For each prefix (n choices), scan A × scan B → O(n³).

**The O(n) solution — Frequency counter:**

```python
class Solution:
    def findThePrefixCommonArray(self, A: list, B: list) -> list:
        n = len(A)
        freq = [0] * (n + 1)             # 1-indexed: freq[v] = times v seen
        result = []
        common = 0
        for i in range(n):
            freq[A[i]] += 1
            if freq[A[i]] == 2:           # Just hit 2 → in both prefixes
                common += 1
            freq[B[i]] += 1
            if freq[B[i]] == 2:
                common += 1
            result.append(common)
        return result
```

**Why correct:** `freq[v]` is incremented when seen in either array. Since each is a permutation, `freq[v]` ≤ 2. The moment it hits 2 → v is in both prefixes.

**Special case A[i] == B[i]:** Both `+= 1` happen same iteration. First brings freq to 1 (no count). Second brings to 2 (count). Handled naturally.

**Set-based O(n) (cleaner reading):**

```python
class Solution:
    def findThePrefixCommonArray(self, A: list, B: list) -> list:
        seen_A, seen_B = set(), set()
        common = 0
        result = []
        for a, b in zip(A, B):
            seen_A.add(a)
            seen_B.add(b)
            if a in seen_B:
                common += 1
            if b in seen_A and a != b:    # Guard against double-counting
                common += 1
            result.append(common)
        return result
```

The `a != b` guard avoids double-counting when both new elements are the same.

**Bitmask version (slick, O(n²) in Python due to popcount):**

```python
class Solution:
    def findThePrefixCommonArray(self, A: list, B: list) -> list:
        mask_A = mask_B = 0
        result = []
        for a, b in zip(A, B):
            mask_A |= 1 << a
            mask_B |= 1 << b
            result.append(bin(mask_A & mask_B).count('1'))
        return result
```

Each prefix's seen-set becomes a 50-bit integer. AND of masks gives common-set; popcount counts elements.

---

## LAYER 2: Worked Examples

**Example 1: `A = [1,3,2,4]`, `B = [3,1,2,4]` → `[0, 2, 3, 4]`**

Frequency counter trace, starting `freq=[0,0,0,0,0]`, `common=0`:

| i | A[i] | freq after A | B[i] | freq after B | hit 2? | common | result |
|---|------|--------------|------|--------------|--------|--------|--------|
| 0 | 1 | [0,1,0,0,0] | 3 | [0,1,0,1,0] | no | 0 | 0 |
| 1 | 3 | [0,1,0,2,0] | 1 | [0,2,0,2,0] | both | 2 | 2 |
| 2 | 2 | [0,2,1,2,0] | 2 | [0,2,2,2,0] | B's 2 | 3 | 3 |
| 3 | 4 | [0,2,2,2,1] | 4 | [0,2,2,2,2] | B's 4 | 4 | 4 |

Result: `[0, 2, 3, 4]` ✓

**Step interpretation:**
- i=0: A={1}, B={3}, common={} → 0
- i=1: A={1,3}, B={3,1}, common={1,3} → 2
- i=2: A={1,3,2}, B={3,1,2}, common={1,2,3} → 3
- i=3: A={1,3,2,4}, B={3,1,2,4}, common={1,2,3,4} → 4

**Example 2: `A = [2,3,1]`, `B = [3,1,2]` → `[0, 1, 3]`**

| i | A[i] | B[i] | hit 2? | common | result |
|---|------|------|--------|--------|--------|
| 0 | 2 | 3 | no | 0 | 0 |
| 1 | 3 | 1 | A's 3 hit 2 | 1 | 1 |
| 2 | 1 | 2 | A's 1, B's 2 | 3 | 3 |

Result: `[0, 1, 3]` ✓

**Edge cases:**
- n=1: A=[1], B=[1] → freq[1] hits 2 immediately → `[1]`
- A == B: every step adds 2 to count → `[1, 2, 3, ..., n]`
- C[n-1] always equals n (both are permutations of same set)

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Triple nested loop (editorial) | O(n³) | O(n) | Slow, clear |
| Set intersection per prefix | O(n²) | O(n) | Build incrementally |
| **Frequency counter** | **O(n)** | **O(n)** | **Optimal** |
| Bitmask + popcount | O(n²) in Python | O(1) for masks | Slick |

**The Medium-difficulty insight:**

This looks like set-intersection (O(n²) at best). The Medium-tier insight: **permutation constraint changes everything**. Every value appears exactly once in each array → `freq[v]` can only go 0 → 1 → 2.

Once you see this:
- Walk both arrays in parallel
- Track total count per value
- When count hits 2 → just became common → increment

**Why permutation is special:**

| Without permutation | With permutation |
|---------------------|------------------|
| `freq[v]` grows unboundedly | `freq[v]` ≤ 2 |
| Need "is v common YET?" | "v common" = `freq[v] == 2` |
| Set intersection per prefix | Two increment counters |

If A had duplicates (e.g., `[1,1,2]`), the trick breaks — `freq[1]` could hit 2 from A alone. Would need separate `freq_A` and `freq_B`.

**Increment-then-check pattern:**

```python
counter[key] += 1
if counter[key] == THRESHOLD:
    do_something()
```

Common in: LRU caches (counter hits 0 → evict), reference counting (counter hits 0 → free), this problem (counter hits 2 → common).

**Why bitmask is slower in Python:** `bin(x).count('1')` is O(bits) per call. n iterations × O(n) per call = O(n²). The explicit counter is O(1) per iteration → O(n) total.

In C++/Java with hardware `popcnt`, bitmask is O(n) — same as counter.

---

## LAYER 4: Interview Variations

• **Arrays with duplicates (not permutations):** Separate `freq_A` and `freq_B`; common when both ≥ 1.
• **Smallest i where C[i] == i+1 (prefixes fully agree):** Linear scan post-build.
• **Difference instead of intersection:** Track XOR of prefixes.
• **Common across K arrays:** `freq[v]` reaches K.
• **Same value at same index:** Different problem — `sum(A[j] == B[j])`.
• **Weighted common (sum of common values):** When v becomes common, add v.
• **Streaming version:** Maintain freq and common as state.
• **Multiple B's against fixed A:** Pre-build A's position map; run counter pass per B.
• **Suffix common array:** Walk in reverse with same trick.
• **Set of common values (not count):** Add `common_set.add(v)` when freq hits 2.
• **n is huge (10^9):** Hash dict instead of array.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Frequency counter, O(n)
from typing import List

class Solution:
    def findThePrefixCommonArray(self, A: List[int], B: List[int]) -> List[int]:
        n = len(A)
        freq = [0] * (n + 1)
        result = []
        common = 0
        for i in range(n):
            freq[A[i]] += 1
            if freq[A[i]] == 2:
                common += 1
            freq[B[i]] += 1
            if freq[B[i]] == 2:
                common += 1
            result.append(common)
        return result
```

**Set-based O(n):**
```python
def findThePrefixCommonArray(A, B):
    seen_A, seen_B = set(), set()
    common = 0
    result = []
    for a, b in zip(A, B):
        seen_A.add(a)
        seen_B.add(b)
        if a in seen_B:
            common += 1
        if b in seen_A and a != b:
            common += 1
        result.append(common)
    return result
```

**Bitmask:**
```python
def findThePrefixCommonArray(A, B):
    mask_A = mask_B = 0
    result = []
    for a, b in zip(A, B):
        mask_A |= 1 << a
        mask_B |= 1 << b
        result.append(bin(mask_A & mask_B).count('1'))
    return result
```

**Editorial — O(n³):**
```python
def findThePrefixCommonArray(A, B):
    n = len(A)
    result = [0] * n
    for i in range(n):
        c = 0
        for a in range(i + 1):
            for b in range(i + 1):
                if A[a] == B[b]:
                    c += 1
                    break
        result[i] = c
    return result
```

---

**Time:** O(n) | **Space:** O(n)

**Pattern flag:** Two signals: (1) **"permutation"** is your green light to exploit "each value appears exactly once per array" — typically via a frequency counter. (2) **"prefix common" / "running intersection"** → maintain incremental state, check threshold crossings. The increment-then-check idiom (counter hits N → do something) is one of the most reusable counting patterns.
