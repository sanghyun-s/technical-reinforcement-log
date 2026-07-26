# 1684. Count the Number of Consistent Strings

**Difficulty:** Easy
**Pattern:** Bit Manipulation / Set Membership
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

Count words in `words` that use only characters appearing in `allowed`. A word is "consistent" if every char in it is in the allowed set.

**Key insight:** Set membership at heart. Three approaches:
1. **Brute force triple loop** (editorial) — O(n × w × a)
2. **Hash set lookup** — O(1) membership check
3. **Bitmask** — represent each string as a 26-bit integer; `word_bits & ~allowed_bits == 0` checks consistency in one op

The bitmask approach turns string-char checks into single integer operations — fastest in practice.

---

## LAYER 1: Line-by-Line Explanation

**Editorial — Brute force triple-nested loop:**

```python
class Solution:
    def countConsistentStrings(self, allowed: str, words: List[str]) -> int:
        consistent_count = 0
        for word in words:                                  # Each word
            is_word_consistent = True
            for char in word:                                # Each char in word
                is_char_allowed = False
                for allowed_char in allowed:                 # Scan allowed
                    if allowed_char == char:
                        is_char_allowed = True
                        break                                # Found — stop
                if not is_char_allowed:
                    is_word_consistent = False
                    break                                    # Bad char — stop
            if is_word_consistent:
                consistent_count += 1
        return consistent_count
```

The two `break` statements are crucial for short-circuiting on early hits and early failures.

**Total work:** O(n × w × a) where n = num words, w = avg word length, a = allowed length.

**Improved — Hash set lookup:**

```python
class Solution:
    def countConsistentStrings(self, allowed: str, words: List[str]) -> int:
        allowed_set = set(allowed)
        count = 0
        for word in words:
            if all(c in allowed_set for c in word):
                count += 1
        return count
```

`c in allowed_set` is O(1) instead of O(a). `all(...)` short-circuits on first False.

**Tightest — set.issubset:**

```python
class Solution:
    def countConsistentStrings(self, allowed: str, words: List[str]) -> int:
        allowed_set = set(allowed)
        return sum(1 for w in words if set(w) <= allowed_set)
```

`set(w) <= allowed_set` is exactly the subset check.

**Slickest — Bitmask:**

```python
class Solution:
    def countConsistentStrings(self, allowed: str, words: List[str]) -> int:
        allowed_mask = 0
        for c in allowed:
            allowed_mask |= 1 << (ord(c) - ord('a'))
        
        count = 0
        for word in words:
            word_mask = 0
            for c in word:
                word_mask |= 1 << (ord(c) - ord('a'))
            if (word_mask & ~allowed_mask) == 0:
                count += 1
        return count
```

- `ord(c) - ord('a')` maps 'a'..'z' to 0..25
- `1 << k` creates a single-bit mask for bit k
- `mask |= 1 << k` sets bit k
- `word_mask & ~allowed_mask` keeps bits in word but NOT in allowed — i.e., forbidden bits used
- If 0 → consistent

---

## LAYER 2: Worked Examples

**Example 1: `allowed = "ab"`, `words = ["ad","bd","aaab","baa","badab"]` → 2**

`allowed_set = {'a', 'b'}`, allowed_mask = `0b11 = 3`.

| word | chars | subset? | consistent? |
|------|-------|---------|-------------|
| "ad" | {a, d} | d ∉ allowed | ❌ |
| "bd" | {b, d} | d ∉ allowed | ❌ |
| "aaab" | {a, b} | ✅ | ✅ |
| "baa" | {a, b} | ✅ | ✅ |
| "badab" | {a, b, d} | d ∉ allowed | ❌ |

Count: **2** ✓

**Bitmask trace for "aaab":**
- 'a' bit = 0, 'b' bit = 1
- After 'a': mask = 0b01
- After 'a' again: 0b01 (idempotent)
- After 'a' again: 0b01
- After 'b': 0b11 = 3
- allowed_mask = 3, ~allowed_mask = ...11111100
- `3 & ~3 = 0` → consistent ✓

**Bitmask trace for "ad":**
- 'a' → 0b01, 'd' (bit 3) → 0b1001 = 9
- `9 & ~3 = 0b1000 = 8` ≠ 0 → ❌

Bit 3 (the 'd' bit) leaks out — that's the forbidden character.

**Example 2: `allowed = "abc"` → all 7 words consistent** ✓

**Example 3: `allowed = "cad"` → 4** (any word with 'b' is rejected)

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Triple loop (editorial) | O(n × w × a) | O(1) | Slowest, clear |
| Hash set + all() | O(n × w + a) | O(a) | Clean, idiomatic |
| set(w) <= allowed_set | O(n × w + a) | O(a + w) | Most Pythonic |
| Bitmask | O(n × w + a) | O(1) | **Fastest in practice** |

**Why bitmask is special:**

Most "is X a subset of Y" problems on a small alphabet can be reframed as bitmask operations:
- Building mask: O(string length)
- Subset test: single AND-NOT, O(1)
- Memory: one integer per set

**Mental model:** a 26-bit integer IS a set of lowercase letters.

**Set algebra → bitmask:**

| Set op | Bitmask |
|--------|---------|
| A ∪ B | A \| B |
| A ∩ B | A & B |
| A − B | A & ~B |
| A ⊆ B | (A & ~B) == 0 |
| \|A\| | A.bit_count() |

**When bitmask beats hash sets:**

- Small fixed alphabet (≤ 64 letters)
- Many subset/superset checks
- Performance-critical code

For Python and small inputs, hash sets are equally fast and clearer.

**Why editorial uses triple loops:** Pedagogy. The problem is filed under "brute force." Common interview phase: start brute force → optimize step by step.

---

## LAYER 4: Interview Variations

• **Count INCONSISTENT strings:** `len(words) - countConsistent(...)`.
• **Return the consistent strings themselves:** `[w for w in words if set(w) <= allowed_set]`.
• **Allowed with repeats (still a set):** `set()` dedupes automatically.
• **Each char has frequency limit:** Use Counter; check element-wise.
• **Word must be subsequence of allowed:** Different — two pointers.
• **Allowed as regex pattern:** `re.fullmatch(pattern, word)`.
• **Uppercase + lowercase + digits:** 62-bit mask (Python handles, or use hash set).
• **Unicode chars:** Bitmask fails (too large alphabet); use hash set or trie.
• **Streaming words:** Maintain allowed_mask; check each in O(word length).
• **Multiple allowed sets per word:** Compare against each set.
• **Required set (must contain certain letters):** `word_mask & required_mask == required_mask`.
• **Find LONGEST consistent word:** Track max length while iterating.
• **K-th longest consistent word:** Filter then sort.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Bitmask, O(1) subset check
class Solution:
    def countConsistentStrings(self, allowed: str, words: List[str]) -> int:
        allowed_mask = 0
        for c in allowed:
            allowed_mask |= 1 << (ord(c) - ord('a'))
        count = 0
        for word in words:
            word_mask = 0
            for c in word:
                word_mask |= 1 << (ord(c) - ord('a'))
            if (word_mask & ~allowed_mask) == 0:
                count += 1
        return count
```

**Pythonic — set subset:**
```python
def countConsistentStrings(allowed, words):
    a = set(allowed)
    return sum(1 for w in words if set(w) <= a)
```

**With all():**
```python
def countConsistentStrings(allowed, words):
    a = set(allowed)
    return sum(1 for w in words if all(c in a for c in w))
```

**One-liner with set difference:**
```python
def countConsistentStrings(allowed, words):
    a = set(allowed)
    return sum(1 for w in words if not (set(w) - a))
```

**Editorial — triple loop:**
```python
def countConsistentStrings(allowed, words):
    count = 0
    for word in words:
        ok = True
        for char in word:
            if char not in allowed:
                ok = False
                break
        if ok:
            count += 1
    return count
```

---

**Time:** O(n × w + a) | **Space:** O(a)

**Pattern flag:** "Check if all chars of X are in set Y, over many X's" → build Y as hash set or 26-bit mask ONCE, then check each X in O(|X|). When alphabet is small and fixed, bitmask trick collapses set ops to single CPU instructions.
