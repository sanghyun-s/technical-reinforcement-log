# 3110. Score of a String

**Difficulty:** Easy
**Pattern:** String / Adjacent Pair Aggregation
**Date Solved:** 2026-06-05
**Status:** ✅

---

## Understanding the Goal

For each adjacent pair of characters, take the absolute difference of their ASCII values. Sum all those differences.

**Key insight:** Same "sliding window of size 2" pattern as yesterday's LC 3173 (Bitwise OR of Adjacent Elements) — just a different operation. Walk the string once, apply `abs(ord(a) - ord(b))` to each adjacent pair, sum.

The only string-specific wrinkle is `ord()` to convert chars → ASCII ints.

---

## LAYER 1: Line-by-Line Explanation

**Editorial — explicit loop:**

```python
class Solution:
    def scoreOfString(self, s: str) -> int:
        score = 0                                          # Running total of ASCII diffs
        for i in range(len(s) - 1):                        # 0 to n-2 (each i has a right neighbor)
            score += abs(ord(s[i]) - ord(s[i + 1]))        # |ASCII(s[i]) - ASCII(s[i+1])|
        return score
```

- `range(len(s) - 1)` — visit indices where a right neighbor exists
- `ord(c)` — char to ASCII int (`'h' → 104`)
- `abs(...)` — direction-agnostic difference

**Pythonic one-liner (yesterday's zip trick):**

```python
class Solution:
    def scoreOfString(self, s: str) -> int:
        # zip(s, s[1:]) yields consecutive char pairs: ('h','e'), ('e','l'), ...
        return sum(abs(ord(a) - ord(b)) for a, b in zip(s, s[1:]))
```

Strings are iterable, so `zip(s, s[1:])` works on characters directly.

---

## LAYER 2: Worked Examples

**Example 1: `s = "hello"` → 13**

ASCII: h=104, e=101, l=108, l=108, o=111.

| i | s[i] | s[i+1] | ord diff | abs |
|---|------|--------|----------|-----|
| 0 | h (104) | e (101) | 3 | 3 |
| 1 | e (101) | l (108) | -7 | 7 |
| 2 | l (108) | l (108) | 0 | 0 |
| 3 | l (108) | o (111) | -3 | 3 |

Sum: 3 + 7 + 0 + 3 = **13** ✓

**Example 2: `s = "zaz"` → 50**

| i | s[i] | s[i+1] | ord diff | abs |
|---|------|--------|----------|-----|
| 0 | z (122) | a (97) | 25 | 25 |
| 1 | a (97) | z (122) | -25 | 25 |

Sum: 50 ✓

The `abs()` is crucial — without it, `25 + (-25) = 0`. The score measures "total movement," not "net movement."

**Walking through `zip(s, s[1:])` for `"hello"`:**

```
s    = "hello"
s[1:] = "ello"
zip yields: ('h','e'), ('e','l'), ('l','l'), ('l','o')
```

4 pairs → 4 differences → sum.

**Edge cases:**
- Min length n=2: `"ab"` → `abs(97-98) = 1`
- All same: `"aaaa"` → 0
- Alternating extremes: `"azaz"` → 75
- Monotonic: `"abc"` → 1 + 1 = 2
- Constraints guarantee n ≥ 2

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Explicit loop | O(n) | O(1) | Most readable |
| `sum + zip` generator | O(n) | O(1) | **Pythonic, preferred** |
| List comp + sum | O(n) | O(n) intermediate | Slightly more memory |

**Why this is the same problem as LC 3173:**

Both follow the **"adjacent pair aggregation"** template:

```python
result = aggregate(op(a, b) for a, b in zip(arr, arr[1:]))
```

- LC 3173: `op = |` (bitwise OR), `aggregate = list`
- LC 3110: `op = lambda a, b: abs(ord(a) - ord(b))`, `aggregate = sum`

Same template, two operations. Recognizing this is the pattern recognition payoff.

**The general template:**

```python
sum(f(a, b) for a, b in zip(seq, seq[1:]))      # sum of pairwise f
[f(a, b) for a, b in zip(seq, seq[1:])]         # list of pairwise f
max(f(a, b) for a, b in zip(seq, seq[1:]))      # max pairwise f
```

**`ord()` + arithmetic — the "string as numbers" pattern:**

- `ord('a') = 97`
- `ord('A') = 65`
- `ord('0') = 48`
- `ord(c) - ord('a')` maps `'a'..'z'` → `0..25` (used in bitmask problems)

**"Score" as a metric:**

This is **total variation** of the string (sum of absolute differences). Low score = smooth/repetitive (`"aaaa"`), high score = jumpy (`"azaz"`). Shows up in signal processing, time-series analysis, path optimization.

---

## LAYER 4: Interview Variations

• **Signed sum (no abs):** Telescopes to `ord(s[0]) - ord(s[-1])`.
• **Squared differences:** Least-squares contexts.
• **Window size k:** `sum(abs(ord(s[i]) - ord(s[i+k])) for i in range(len(s) - k))`.
• **MAX adjacent diff:** Replace `sum` with `max`.
• **Position of max-diff pair:** `max(range(len(s)-1), key=lambda i: ...)`.
• **Custom char-to-value mapping:** Replace `ord()` with dict lookup.
• **Unicode chars:** `ord()` works; very different distances.
• **List of integers (not string):** Drop `ord()`. "Total variation of a sequence."
• **MINIMUM score after rearranging:** Sort first; equals `ord(max) - ord(min)` by telescoping.
• **Cumulative score per prefix:** `itertools.accumulate`.
• **LONGEST low-variation substring:** Sliding window; shrink when running score exceeds K.
• **Streaming version:** Keep last char as state; emit on new arrival.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Pythonic one-liner
class Solution:
    def scoreOfString(self, s: str) -> int:
        return sum(abs(ord(a) - ord(b)) for a, b in zip(s, s[1:]))
```

**Editorial — explicit loop:**
```python
def scoreOfString(s):
    score = 0
    for i in range(len(s) - 1):
        score += abs(ord(s[i]) - ord(s[i + 1]))
    return score
```

**List comp + sum:**
```python
def scoreOfString(s):
    return sum([abs(ord(s[i]) - ord(s[i+1])) for i in range(len(s)-1)])
```

**Cumulative running score (for debugging):**
```python
from itertools import accumulate
def scoreOfString(s):
    diffs = (abs(ord(a) - ord(b)) for a, b in zip(s, s[1:]))
    return list(accumulate(diffs))[-1]
```

---

**Time:** O(n) | **Space:** O(1)

**Pattern flag:** "Aggregate over adjacent pairs of a sequence" → `aggregate(op(a, b) for a, b in zip(seq, seq[1:]))`. For string problems, layer `ord()` on top to work with ASCII codes. One of the most reused patterns in array/string problems.
