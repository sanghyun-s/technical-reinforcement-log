# 2220. Minimum Bit Flips to Convert Number

**Difficulty:** Easy
**Pattern:** Bit Manipulation / XOR + Popcount
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

Count bit positions where `start` and `goal` differ. That's the minimum flips needed.

**Key insight:** `start XOR goal` produces a number where bits are 1 exactly at differing positions. Answer = **popcount(start XOR goal)** — a one-liner.

This problem is literally Hamming Distance (LC 461) in disguise. Once you see XOR + popcount, this collapses to one line.

---

## LAYER 1: Line-by-Line Explanation

**One-liner — XOR + popcount:**

```python
class Solution:
    def minBitFlips(self, start: int, goal: int) -> int:
        return bin(start ^ goal).count('1')
```

- `start ^ goal` — XOR. Bit b is 1 iff start and goal disagree at bit b.
- `bin(...)` — convert to binary string like `'0b1101'`
- `.count('1')` — count 1-characters (popcount)

**Python 3.10+ built-in:**

```python
class Solution:
    def minBitFlips(self, start: int, goal: int) -> int:
        return (start ^ goal).bit_count()
```

Maps to CPU's `POPCNT` instruction.

**Editorial — Bit-by-bit comparison:**

```python
class Solution:
    def minBitFlips(self, start: int, goal: int) -> int:
        count = 0
        while start > 0 or goal > 0:
            if (start & 1) != (goal & 1):
                count += 1
            start >>= 1
            goal >>= 1
        return count
```

- `while start > 0 or goal > 0` — OR (not AND) because either might still have bits
- `start & 1` — extract lowest bit
- `>>= 1` — drop the bit we examined

**Brian Kernighan variant (fastest for sparse XOR):**

```python
class Solution:
    def minBitFlips(self, start: int, goal: int) -> int:
        xor = start ^ goal
        count = 0
        while xor:
            xor &= xor - 1
            count += 1
        return count
```

Iterates once per set bit (not per bit position).

---

## LAYER 2: Worked Examples

**Example 1: `start = 10, goal = 7` → 3**

- 10 = `1010`, 7 = `0111`
- XOR: `1010 ^ 0111 = 1101 = 13`
- popcount(13) = 3 ✓

**Editorial loop trace:**

| iter | start | goal | start&1 | goal&1 | diff? | count |
|------|-------|------|---------|--------|-------|-------|
| 1 | 1010 | 0111 | 0 | 1 | yes | 1 |
| 2 | 0101 | 0011 | 1 | 1 | no | 1 |
| 3 | 0010 | 0001 | 0 | 1 | yes | 2 |
| 4 | 0001 | 0000 | 1 | 0 | yes | 3 |
| 5 | 0000 | 0000 | — | — | exit | 3 |

**Example 2: `start = 3, goal = 4` → 3**

- 3 = `011`, 4 = `100`
- XOR: `111 = 7`
- popcount = 3 ✓

Special case: 3 and 4 share no bits → XOR = sum = 7.

**Brian Kernighan trace for Example 1 (xor=13=`1101`):**
- iter 1: `1101 & 1100 = 1100`, count=1
- iter 2: `1100 & 1011 = 1000`, count=2
- iter 3: `1000 & 0111 = 0000`, count=3
- exit

**Edge cases:**
- start == goal: XOR=0, count=0
- One is 0: count = popcount of other
- Both 0: count=0
- 10⁹ values: up to 30 bits, still O(1) effective

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| `bin(x ^ y).count('1')` | O(log max) | O(log max) string | **Pythonic one-liner** |
| `(x ^ y).bit_count()` | O(log max) | O(1) | **Optimal Python 3.10+** |
| Editorial bit-by-bit loop | O(log max) | O(1) | Educational |
| Brian Kernighan on XOR | O(set bits) | O(1) | **Fastest sparse** |

**The XOR + popcount template (memorize):**

> **"Count positions where two values differ" → `popcount(a ^ b)`**

Solves:
- Hamming distance (LC 461)
- Minimum bit flips (this problem)
- Error-correcting codes
- Genetic sequence comparison (after encoding)
- Perceptual image hashing

XOR is the **"disagreement detector"**: bit b of `a ^ b` is 1 iff a and b disagree at b.

**Why XOR, not OR or AND:**

| Op | Bit b means |
|----|-------------|
| `a & b` | Both have bit b |
| `a \| b` | At least one has bit b |
| `a ^ b` | Exactly one has bit b (disagree) |

We want disagree → XOR.

**Why editorial uses manual loop:** Pedagogy. The hints walk through "compare each bit" → "use XOR." The editorial implements hint #1; the one-liner takes hint #2.

**Why `bit_count()` preferred:**
- Hardware-accelerated POPCNT
- No string conversion overhead
- Cleaner code

---

## LAYER 4: Interview Variations

• **Hamming Distance (LC 461):** Literally identical. Same `popcount(x ^ y)`.
• **Total Hamming Distance across array (LC 477):** For each bit position, count `k` values with bit set; contribution = `k × (n-k)`.
• **Costs per bit position:** `sum(cost[i] for i in range(30) if ((start^goal) >> i) & 1)`.
• **Forbidden bit positions:** Check XOR for any forbidden bits set.
• **Flips AND swaps allowed:** Different problem (cheaper than pure flips).
• **Bit flips on strings:** Walk both, count differing chars.
• **Find value K flips from start:** Enumerate `C(n, K)` candidates.
• **Closest value with popcount == K:** Greedy bit manipulation.
• **Negative values:** Two's complement; XOR still works (Python bigints).
• **Streaming pairs:** Each O(1).
• **Bit flips to make palindrome:** Different — check pairs `(bit_i, bit_n-1-i)`.
• **Bits set in EITHER:** `popcount(a | b)`.
• **Bits set in BOTH:** `popcount(a & b)`.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Python 3.10+
class Solution:
    def minBitFlips(self, start: int, goal: int) -> int:
        return (start ^ goal).bit_count()
```

**Universal Python:**
```python
class Solution:
    def minBitFlips(self, start: int, goal: int) -> int:
        return bin(start ^ goal).count('1')
```

**Editorial — bit-by-bit:**
```python
def minBitFlips(start, goal):
    count = 0
    while start > 0 or goal > 0:
        if (start & 1) != (goal & 1):
            count += 1
        start >>= 1
        goal >>= 1
    return count
```

**Brian Kernighan trick:**
```python
def minBitFlips(start, goal):
    xor = start ^ goal
    count = 0
    while xor:
        xor &= xor - 1
        count += 1
    return count
```

---

**Time:** O(log(max)) | **Space:** O(1)

**Pattern flag:** "Compare bits of two values" → **XOR them, then popcount.** This `popcount(a ^ b)` template is one of the most reused tricks in bit manipulation. Whenever a problem talks about "differing bits," "flips needed," "Hamming distance," or "positions that disagree" — XOR is the first reach, popcount the second.
