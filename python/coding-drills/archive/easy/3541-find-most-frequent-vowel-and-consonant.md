# 3541. Find Most Frequent Vowel and Consonant

**Difficulty:** Easy
**Pattern:** Hash Table / Counter / Conditional Max
**Date Solved:** 2026-06-07
**Status:** ✅

---

## Understanding the Goal

Count each character in `s`. Find:
- Max frequency among **vowels** (`a`, `e`, `i`, `o`, `u`)
- Max frequency among **consonants** (everything else)

Return their sum. If no vowels/consonants exist, treat as 0.

**Key insight:** Three sub-steps:
1. **Count** all characters (Counter)
2. **Partition** by vowel vs consonant
3. **Take max** with default of 0 for empty case

The editorial uses `max(..., default=0)` — the elegant way to handle "no items match the filter."

---

## LAYER 1: Line-by-Line Explanation

### Editorial — two `max` calls with conditional generators

```python
from collections import Counter

class Solution:
    def maxFreqSum(self, s: str) -> int:
        # Step 1: Count every character
        # Counter("successes") = {'s': 4, 'u': 1, 'c': 2, 'e': 2}
        mp = Counter(s)
        
        # Step 2a: Max frequency among VOWELS
        # Generator yields mp[ch] only when ch is a vowel
        # default=0 handles "no vowels" case (generator yields nothing)
        vowel = max((mp[ch] for ch in mp if ch in "aeiou"), default=0)
        
        # Step 2b: Max frequency among CONSONANTS
        # Same pattern with OPPOSITE filter
        consonant = max((mp[ch] for ch in mp if ch not in "aeiou"), default=0)
        
        # Step 3: Sum the maxima
        return vowel + consonant
```

**Decoding `max(..., default=0)`:**

`max()` raises ValueError on an empty iterable:
```python
max([])  # ValueError
```

But `max(iterable, default=value)` returns `value` when empty:
```python
max([], default=0)  # Returns 0
```

This is **the** clean way to handle "find max if any exist, else sentinel." Replaces verbose:
```python
vowels_only = [mp[ch] for ch in mp if ch in "aeiou"]
vowel = max(vowels_only) if vowels_only else 0
```

**Why generator (not list comp):**

Lazy — yields values without building intermediate list. Memory-efficient for large alphabets.

**Why iterate `mp` (not `s`):**

Each distinct character appears at most once in `mp`. Iterating `s` directly would re-process duplicates: for `s = "aaaa"`, you'd evaluate `mp['a']` four times.

### Alternative — single pass through Counter

```python
from collections import Counter

class Solution:
    def maxFreqSum(self, s: str) -> int:
        mp = Counter(s)
        max_vowel = 0
        max_consonant = 0
        
        for ch, freq in mp.items():
            if ch in "aeiou":
                if freq > max_vowel:
                    max_vowel = freq
            else:
                if freq > max_consonant:
                    max_consonant = freq
        
        return max_vowel + max_consonant
```

Same complexity, more explicit. Easier to extend with debug prints or more categories.

### Tighter with set for vowel lookup

```python
from collections import Counter

class Solution:
    def maxFreqSum(self, s: str) -> int:
        mp = Counter(s)
        vowels = set("aeiou")                   # Hash set for O(1) lookup
        
        vowel_max = max((c for ch, c in mp.items() if ch in vowels), default=0)
        cons_max = max((c for ch, c in mp.items() if ch not in vowels), default=0)
        
        return vowel_max + cons_max
```

For only 5 vowels, `in "aeiou"` is already fast. But the habit of building a set scales.

### Manual two-array tally (no Counter)

```python
class Solution:
    def maxFreqSum(self, s: str) -> int:
        freq = [0] * 26                         # freq[0]='a', ..., freq[25]='z'
        for ch in s:
            freq[ord(ch) - ord('a')] += 1
        
        vowel_indices = {ord(c) - ord('a') for c in "aeiou"}  # {0, 4, 8, 14, 20}
        
        vowel_max = 0
        cons_max = 0
        for i, count in enumerate(freq):
            if i in vowel_indices:
                vowel_max = max(vowel_max, count)
            else:
                cons_max = max(cons_max, count)
        
        return vowel_max + cons_max
```

Avoids Counter import.

---

## LAYER 2: Worked Examples

### Example 1: `s = "successes"` → 6

`Counter("successes") = {'s': 4, 'u': 1, 'c': 2, 'e': 2}`

| ch | freq | vowel? | tracked as |
|----|------|--------|------------|
| 's' | 4 | no | consonant |
| 'u' | 1 | yes | vowel |
| 'c' | 2 | no | consonant |
| 'e' | 2 | yes | vowel |

- Max vowel: max(1, 2) = **2** (the 'e's)
- Max consonant: max(4, 2) = **4** (the 's')

Total: 2 + 4 = **6** ✓

### Example 2: `s = "aeiaeia"` → 3

`Counter("aeiaeia") = {'a': 3, 'e': 2, 'i': 2}`

All chars are vowels.

- Max vowel: max(3, 2, 2) = **3**
- Max consonant: generator yields nothing → `default=0` → **0**

Total: 3 + 0 = **3** ✓

This is where `default=0` earns its keep — without it, `max()` would raise an error.

### Trace generator + `max(default=0)` on Example 2

For consonants:
```
mp = {'a': 3, 'e': 2, 'i': 2}
Generator: (mp[ch] for ch in mp if ch not in "aeiou")
  'a' in "aeiou" → skip
  'e' in "aeiou" → skip
  'i' in "aeiou" → skip
Generator yields nothing.
max(<empty>, default=0) → 0 ✓
```

### Edge cases
- All vowels: consonant defaults to 0
- All consonants: vowel defaults to 0
- Single char vowel: vowel=1, consonant=0, sum=1
- All same vowel char ("aaaa"): vowel=4, consonant=0
- Tie between vowels: max picks the highest (ties broken arbitrarily, problem allows)

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Counter + `max(default=0)` | O(n) | O(unique chars) | **Editorial; preferred** |
| Single-pass conditional | O(n) | O(unique chars) | Easy to extend |
| Counter + most_common early-exit | O(n) | O(unique chars) | Marginally faster |
| Manual 26-int array | O(n) | O(26) | No Counter import |

For n ≤ 100, all instant. Choice is about clarity.

**The `max(iterable, default=value)` idiom — memorize this:**

```python
max((x for x in items if condition(x)), default=0)
min((x for x in items if condition(x)), default=float('inf'))
```

The clean way to compute "max/min of filtered subset, with sentinel when nothing matches." The `default=` keyword was added in Python 3.4 specifically to streamline this pattern.

**Generators vs list comprehensions:**

| Form | When to use |
|------|-------------|
| `[x for x in items]` | Need as list (will index, len, iterate twice) |
| `(x for x in items)` | One-shot in `sum`, `max`, `min`, `any`, `all` |

Generators save memory. For tiny inputs negligible; habit is good.

**The "boolean partition + max" pattern:**

> "For each item, classify into category. Find max within each category."

Examples:
- Max stock price on weekdays vs weekends
- Max test score by gender
- Highest-paid employee per department (with `groupby`)

Skeleton:
```python
category_a_max = max((value(x) for x in items if classify(x) == 'A'), default=base)
category_b_max = max((value(x) for x in items if classify(x) == 'B'), default=base)
```

For K categories, generalize to a dict.

**Counter vs manual array:**

- **Counter:** Python-native, supports `.most_common()` and arithmetic
- **Manual array:** Slightly faster constant factor, no import

For 26 lowercase letters, either fine. For generic alphabet (Unicode, mixed case), Counter more flexible.

---

## LAYER 4: Interview Variations

• **Return chars (not just frequencies):** Use `max(..., key=mp.get)`.
• **Top K vowels + top K consonants:** Sort by freq descending; take top K.
• **Frequency totals (not max):** `sum(c for ch, c in mp.items() if ch in "aeiou")`.
• **Difference instead of sum:** Trivial change.
• **More categories (vowels / consonants / digits / other):** Generalize partition.
• **Stream version:** Running max per category.
• **Case-insensitive:** `s.lower()` first.
• **Track ties (all max-freq chars):** Collect chars with `freq == max_freq`.
• **Freq > K only:** Add second filter: `if ch in "aeiou" and mp[ch] > K`.
• **Weighted (vowels count double):** Multiply in generator.
• **Two-string variant:** Compute Counters for both; subtract.
• **Non-English vowels:** Custom set. Multilingual: Unicode categories.
• **Most frequent letter overall:** `Counter(s).most_common(1)[0]`.
• **K most frequent letters:** `Counter(s).most_common(K)`.
• **Rarest vowel and rarest consonant:** Replace `max` with `min`; default `float('inf')`.
• **Bigrams:** `Counter(zip(s, s[1:]))` for pair frequencies.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — editorial style with max(default=0)
from collections import Counter

class Solution:
    def maxFreqSum(self, s: str) -> int:
        mp = Counter(s)
        vowel = max((mp[ch] for ch in mp if ch in "aeiou"), default=0)
        consonant = max((mp[ch] for ch in mp if ch not in "aeiou"), default=0)
        return vowel + consonant
```

**Explicit loop (most readable):**
```python
from collections import Counter

def maxFreqSum(s):
    mp = Counter(s)
    max_v = max_c = 0
    for ch, freq in mp.items():
        if ch in "aeiou":
            max_v = max(max_v, freq)
        else:
            max_c = max(max_c, freq)
    return max_v + max_c
```

**With set for vowel lookup:**
```python
from collections import Counter

def maxFreqSum(s):
    mp = Counter(s)
    vowels = set("aeiou")
    v = max((c for ch, c in mp.items() if ch in vowels), default=0)
    cons = max((c for ch, c in mp.items() if ch not in vowels), default=0)
    return v + cons
```

**Manual 26-int array (no imports):**
```python
def maxFreqSum(s):
    freq = [0] * 26
    for ch in s:
        freq[ord(ch) - ord('a')] += 1
    vowel_idx = {ord(c) - ord('a') for c in "aeiou"}
    
    v = max((c for i, c in enumerate(freq) if i in vowel_idx), default=0)
    cons = max((c for i, c in enumerate(freq) if i not in vowel_idx), default=0)
    return v + cons
```

**With `most_common` and early exit:**
```python
from collections import Counter

def maxFreqSum(s):
    mp = Counter(s)
    v = c = 0
    for ch, freq in mp.most_common():
        if ch in "aeiou" and not v:
            v = freq
        elif ch not in "aeiou" and not c:
            c = freq
        if v and c:
            break
    return v + c
```

---

**Time:** O(n) | **Space:** O(unique chars) — at most 26

**Pattern flag:** "Partition items into categories, find max within each" → conditional generators + `max(..., default=0)`. The `default=` keyword is the elegant way to handle "what if no items match the filter" without explicit empty-list check. Memorize this idiom — one of the cleanest patterns in modern Python; appears constantly in real-world data processing.
