# 3146. Permutation Difference between Two Strings

**Difficulty:** Easy
**Pattern:** Hash Table / Index Lookup / Sum of Differences
**Date Solved:** 2026-06-09
**Status:** ✅

---

## Understanding the Goal

Two strings `s` and `t` where every character is unique. `t` is a permutation of `s`. For each character, find its index in both; sum the absolute differences.

**Key insight:** Each character has one position in each string. Build a **lookup table** (`char → index`) for one string in O(n), then iterate the other in O(n), summing `|i_s - i_t|`.

This is the **"hash map for fast position lookup"** pattern — same family as LC 3846 (typing on a keyboard) with `char → (row, col)`. Here it's `char → index`.

---

## LAYER 1: Line-by-Line Explanation

### Pythonic — dict comprehension + sum

```python
class Solution:
    def findPermutationDifference(self, s: str, t: str) -> int:
        # Build map char → its index in s
        # {ch: idx for idx, ch in enumerate(s)} — canonical "indexed dict"
        index_in_s = {ch: i for i, ch in enumerate(s)}
        
        # For each (i, ch) in t, look up ch's index in s, take abs diff, sum
        # Each char's contribution is independent → "stateless map → sum" idiom
        return sum(abs(index_in_s[ch] - i) for i, ch in enumerate(t))
```

**Decoding:**
- `enumerate(s)` yields `(0, 'a'), (1, 'b'), ...`
- Dict comp flips it: `{'a': 0, 'b': 1, ...}` — keyed by character
- For each char in `t`, look up its `s`-index in O(1)
- Sum absolute differences

### Explicit loop

```python
class Solution:
    def findPermutationDifference(self, s: str, t: str) -> int:
        index_in_s = {}
        for i, ch in enumerate(s):
            index_in_s[ch] = i
        
        total = 0
        for i, ch in enumerate(t):
            total += abs(index_in_s[ch] - i)
        return total
```

### Two-dict version

```python
class Solution:
    def findPermutationDifference(self, s: str, t: str) -> int:
        index_in_s = {ch: i for i, ch in enumerate(s)}
        index_in_t = {ch: i for i, ch in enumerate(t)}
        
        return sum(abs(index_in_s[ch] - index_in_t[ch]) for ch in index_in_s)
```

### Using `.index()` (O(n²) — DON'T)

```python
class Solution:
    def findPermutationDifference(self, s: str, t: str) -> int:
        # WARNING: O(n²) — for each char, scans for it
        return sum(abs(i - t.index(ch)) for i, ch in enumerate(s))
```

Works for n ≤ 26 but doesn't generalize. **Demonstrates why hash map matters at scale.**

### 26-int array (tightest space)

```python
class Solution:
    def findPermutationDifference(self, s: str, t: str) -> int:
        idx = [0] * 26
        for i, ch in enumerate(s):
            idx[ord(ch) - ord('a')] = i
        return sum(abs(idx[ord(ch) - ord('a')] - i) for i, ch in enumerate(t))
```

For lowercase letters, array beats dict (no hashing overhead).

---

## LAYER 2: Worked Examples

### Example 1: `s = "abc"`, `t = "bac"` → 2

Build `index_in_s = {'a': 0, 'b': 1, 'c': 2}`.

| i (in t) | ch | index_in_s[ch] | abs diff |
|----------|-----|----------------|----------|
| 0 | 'b' | 1 | 1 |
| 1 | 'a' | 0 | 1 |
| 2 | 'c' | 2 | 0 |

Sum: **2** ✓

### Example 2: `s = "abcde"`, `t = "edbac"` → 12

Build `index_in_s = {'a': 0, 'b': 1, 'c': 2, 'd': 3, 'e': 4}`.

| i | ch | index_in_s[ch] | abs diff |
|---|-----|----------------|----------|
| 0 | 'e' | 4 | 4 |
| 1 | 'd' | 3 | 2 |
| 2 | 'b' | 1 | 1 |
| 3 | 'a' | 0 | 3 |
| 4 | 'c' | 2 | 2 |

Sum: 4 + 2 + 1 + 3 + 2 = **12** ✓

### Edge cases

- **Identical strings:** All diffs 0 → return 0
- **Length 1:** Char at same position → 0
- **Length 2 swap ("ab" → "ba"):** 1 + 1 = 2
- **Full reverse ("abcde" → "edcba"):** 4 + 2 + 0 + 2 + 4 = 12
- **Max n=26:** Constant work — ≤ 26 dict entries

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **Hash map + sum** | **O(n)** | **O(n)** | **Preferred** |
| Two hash maps | O(n) | O(n) | Slight overhead |
| `.index()` per char | O(n²) | O(1) | Tiny n only |
| 26-int array | O(n) | O(1) | Tightest |

**The "hash map for position lookup" pattern:**

> "Given two collections sharing elements, compare positions/properties of each shared element."

Skeleton:
```python
lookup = {item: idx for idx, item in enumerate(collection_a)}
result = aggregate(f(lookup[item], idx) for idx, item in enumerate(collection_b))
```

Applied in:
- **LC 3846 (Day 9):** keyboard `char → (row, col)`
- **LC 3146 (this):** `char → index`
- **LC 1 Two Sum:** value → index for complement lookup
- Any "find me where X is" problem

`lookup[ch]` is O(1) — what makes this scalable.

**The dict comprehension is so clean:**

```python
{ch: i for i, ch in enumerate(s)}
```

**Inverts** an indexed sequence — `[a, b, c]` → `{a: 0, b: 1, c: 2}`. Used constantly:
- Position lookups
- "Name → ID" maps
- Inverting one-to-one mappings

**Why `t` being a permutation matters:**

Guarantees every char in `s` appears exactly once in `t`. Without this:
- Char in `t` might not be in `s` → `KeyError`
- Char might appear multiple times → ambiguous index
- Strings might have different lengths

In real-world variant, you'd need `dict.get()` with default, or multi-position list.

**Connection to "stateless map → sum":**

`sum(abs(...) for i, ch in enumerate(t))` is the "stateless map → sum" idiom — each `(i, ch)` contributes independently. Seen in LC 2011, 3110, 3945, etc.

**Why this is conceptually complete:**

Touches every fundamental of hash table family in 2 lines:
- Construction (dict comp)
- Lookup (O(1) by key)
- Aggregation (sum over generator)

If you read/write this in <30 seconds, the core is internalized.

---

## LAYER 4: Interview Variations

• **Per-character contributions (list, not sum):** `[abs(index_in_s[ch] - i) for i, ch in enumerate(t)]`.
• **Char with largest displacement:** `max(t, key=lambda ch: abs(index_in_s[ch] - t.index(ch)))`.
• **Manhattan-style distance (2D grids):** Same pattern with `(row, col)` — LC 3846.
• **Squared differences:** Replace `abs` with `**2`.
• **Average displacement:** `total / len(s)`.
• **Chars that DIDN'T move:** `[ch for ch in s if idx_s[ch] == idx_t[ch]]`.
• **Both with repeats:** Need list of indices; aggregation ambiguous.
• **`t` is subset (not permutation):** `dict.get(ch, default)`.
• **Largest diff achievable with k swaps:** Different/harder problem.
• **Streaming chars:** Maintain running counts; recompute when full.
• **Comparison with Hamming distance:** Hamming counts MISMATCHES; this sums DISTANCES.
• **Edit distance (LC 72):** General case — allows insert/delete/replace.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Pythonic one-liner
class Solution:
    def findPermutationDifference(self, s: str, t: str) -> int:
        index_in_s = {ch: i for i, ch in enumerate(s)}
        return sum(abs(index_in_s[ch] - i) for i, ch in enumerate(t))
```

**Explicit loop:**
```python
def findPermutationDifference(s, t):
    index_in_s = {}
    for i, ch in enumerate(s):
        index_in_s[ch] = i
    total = 0
    for i, ch in enumerate(t):
        total += abs(index_in_s[ch] - i)
    return total
```

**Two-dict approach:**
```python
def findPermutationDifference(s, t):
    idx_s = {ch: i for i, ch in enumerate(s)}
    idx_t = {ch: i for i, ch in enumerate(t)}
    return sum(abs(idx_s[ch] - idx_t[ch]) for ch in idx_s)
```

**26-int array (tightest space):**
```python
def findPermutationDifference(s, t):
    idx = [0] * 26
    for i, ch in enumerate(s):
        idx[ord(ch) - ord('a')] = i
    return sum(abs(idx[ord(ch) - ord('a')] - i) for i, ch in enumerate(t))
```

**One-liner with `str.find` (works, slower for larger n):**
```python
def findPermutationDifference(s, t):
    return sum(abs(s.find(ch) - i) for i, ch in enumerate(t))
```

**Per-character contributions (debugging):**
```python
def findPermutationDifference(s, t):
    idx_s = {ch: i for i, ch in enumerate(s)}
    contribs = [(ch, abs(idx_s[ch] - i)) for i, ch in enumerate(t)]
    return sum(c for _, c in contribs)
```

---

**Time:** O(n) | **Space:** O(n) for dict (O(26) max for lowercase)

**Pattern flag:** "Given two collections sharing elements, compute something per shared element via positions" → **hash map keyed by shared element, valued by position in one collection**; iterate other and look up in O(1). The **"hash map for fast lookup"** family — same skeleton as LC 1 Two Sum (value → index) and LC 3846 (char → (row, col)).

Dict comp `{ch: i for i, ch in enumerate(s)}` is the canonical "indexed dict" — inverts an indexed sequence. Combined with "stateless map → sum," entire solution = 2 lines.

**Microcosm of hash table family** — construction, O(1) lookup, aggregation, all in 2 lines. If this clicks instantly, hash table family is fully internalized.
