# 3945. Digit Frequency Score

**Difficulty:** Easy
**Pattern:** Hash Table / Digit Manipulation / Counter
**Date Solved:** 2026-06-07
**Status:** ✅

---

## Understanding the Goal

For each distinct digit `d` in `n`, compute `d × freq(d)` and sum these contributions.

**Key insight:** Two micro-decisions:
1. **How to count digit frequencies** — `collections.Counter` is the canonical Python tool
2. **How to extract digits** — convert `n` to a string OR repeatedly mod-and-divide

Once you have `{digit: count}`, the answer is `sum(d * count for d, count in counter.items())`.

**Tiny observation:** Digit `0` contributes `0 × count = 0` no matter what. You can skip it — though including it doesn't hurt (just adds 0).

---

## LAYER 1: Line-by-Line Explanation

### Pythonic one-liner with Counter

```python
from collections import Counter

class Solution:
    def digitFrequencyScore(self, n: int) -> int:
        # Counter(str(n)) builds {char_digit: count} like Counter("122") = {'1': 1, '2': 2}
        # Then for each (digit_char, count) pair, multiply int(d) * count and sum
        return sum(int(d) * count for d, count in Counter(str(n)).items())
```

- `str(n)` — convert int to string (e.g., `122` → `"122"`)
- `Counter(str(n))` — builds a dict-like object counting each character
- `.items()` — yields `(digit_char, count)` pairs
- `int(d) * count` — turn `'2' → 2` and multiply by frequency
- `sum(...)` — total all contributions

### Explicit loop (most readable)

```python
class Solution:
    def digitFrequencyScore(self, n: int) -> int:
        from collections import Counter
        
        freq = Counter(str(n))                  # {char_digit: count}
        score = 0
        for digit_char, count in freq.items():
            digit = int(digit_char)             # Convert '2' to 2
            score += digit * count              # Add contribution
        return score
```

### Without Counter — manual digit extraction (mod/divide)

```python
class Solution:
    def digitFrequencyScore(self, n: int) -> int:
        freq = [0] * 10                         # Array indexed 0..9
        
        # Extract each digit from right to left
        while n > 0:
            digit = n % 10                      # Last digit (122 % 10 = 2)
            freq[digit] += 1                    # Count it
            n //= 10                            # Drop the last digit (122 // 10 = 12)
        
        # Sum d * freq[d] for each digit
        score = 0
        for d in range(10):
            score += d * freq[d]
        return score
```

The mod/divide pattern works in any language without string conversion — useful in C/C++/Java.

### Skipping zero (micro-optimization)

```python
from collections import Counter

class Solution:
    def digitFrequencyScore(self, n: int) -> int:
        return sum(int(d) * count for d, count in Counter(str(n)).items() if d != '0')
```

---

## LAYER 2: Worked Examples

### Example 1: `n = 122` → 5

`Counter("122")` = `{'1': 1, '2': 2}`

| d | count | d × count |
|---|-------|-----------|
| 1 | 1 | 1 |
| 2 | 2 | 4 |

Sum: 1 + 4 = **5** ✓

### Example 2: `n = 101` → 2

`Counter("101")` = `{'1': 2, '0': 1}`

| d | count | d × count |
|---|-------|-----------|
| 1 | 2 | 2 |
| 0 | 1 | 0 |

Sum: 2 + 0 = **2** ✓

The digit `0` shows up but contributes nothing — why "skip zero" is valid.

### Mod/divide trace for `n = 122`

| iter | n before | n % 10 | n after `//= 10` | freq |
|------|----------|--------|-------------------|------|
| 1 | 122 | 2 | 12 | [0,0,1,0,...] |
| 2 | 12 | 2 | 1 | [0,0,2,0,...] |
| 3 | 1 | 1 | 0 | [0,1,2,0,...] |

Final score: 0*0 + 1*1 + 2*2 = 5 ✓

### Edge cases
- Single digit: `Counter("5") = {'5': 1}` → score = 5
- `n = 1`: score = 1
- All same digit (999): `{'9': 3}` → 27
- All distinct (1234): each once → 1+2+3+4 = 10
- Many zeros (1000): `{'1': 1, '0': 3}` → 1
- Max constraint (n = 10⁹): at most 10 digits → tiny work

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| `Counter(str(n))` | O(d) | O(d) | **Pythonic, preferred** |
| Manual dict + `str(n)` | O(d) | O(d) | Counter-free Python |
| Mod/divide + array | O(d) | O(10) | Language-agnostic |

`d ≤ 10` for `n ≤ 10⁹` — essentially O(1). Choice is about code style, not performance.

**The Counter pattern (worth fully internalizing):**

`collections.Counter` is one of Python's most-used standard tools:

```python
Counter("122")                # {'1': 1, '2': 2}
Counter([1, 2, 2, 3])         # {1: 1, 2: 2, 3: 1}
Counter({'a': 3, 'b': 1})     # {'a': 3, 'b': 1}
```

Useful methods:
- `.most_common(k)` — top k by count
- `.elements()` — iterator that repeats each key its count times
- Arithmetic: `c1 + c2`, `c1 - c2`, `c1 & c2`, `c1 | c2`

Dict subclass with default-zero semantics — `Counter[missing_key]` returns 0 instead of raising KeyError.

**Two ways to extract digits:**

| Approach | Best for |
|----------|----------|
| `str(n)` + iterate chars | Python (string ops are clean) |
| `n % 10` + `n //= 10` | C/C++/Java (avoids string allocation) |

In Python, `str(n)` is so fast and clean there's rarely a reason to use mod/divide.

**The "stateless map → sum" pattern:**

```python
sum(int(d) * count for d, count in Counter(str(n)).items())
```

Same shape as LC 2011 (`sum(±1 for op in operations)`) — each input contributes independently. When you see this, the one-liner is natural.

---

## LAYER 4: Interview Variations

• **Score using `d²` or `d³`:** Replace `int(d) * count` with `int(d)**2 * count`.
• **Weighted by position:** `enumerate(str(n))` for positional info.
• **Max digit frequency:** `max(Counter(str(n)).values())`.
• **Most frequent digit:** `Counter(str(n)).most_common(1)[0][0]`.
• **Score in different base:** Use `bin(n)[2:]`, `oct(n)[2:]`, etc.
• **All digits equal?:** `len(set(str(n))) == 1`.
• **Count distinct digits:** `len(set(str(n)))`.
• **Sum of digits:** `sum(int(d) for d in str(n))`.
• **Product of non-zero digits:** `math.prod(int(d) for d in str(n) if d != '0')`.
• **Streaming version:** Maintain Counter incrementally.
• **n as a string already:** Skip `str(n)`, use directly.
• **Negative n:** `abs(n)` first.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Pythonic one-liner
from collections import Counter

class Solution:
    def digitFrequencyScore(self, n: int) -> int:
        return sum(int(d) * count for d, count in Counter(str(n)).items())
```

**Explicit loop:**
```python
from collections import Counter

def digitFrequencyScore(n):
    freq = Counter(str(n))
    score = 0
    for digit_char, count in freq.items():
        score += int(digit_char) * count
    return score
```

**Mod/divide (language-agnostic):**
```python
def digitFrequencyScore(n):
    freq = [0] * 10
    while n > 0:
        freq[n % 10] += 1
        n //= 10
    return sum(d * freq[d] for d in range(10))
```

**Without Counter (manual dict):**
```python
def digitFrequencyScore(n):
    freq = {}
    for c in str(n):
        freq[c] = freq.get(c, 0) + 1
    return sum(int(d) * c for d, c in freq.items())
```

**With defaultdict:**
```python
from collections import defaultdict

def digitFrequencyScore(n):
    freq = defaultdict(int)
    for c in str(n):
        freq[c] += 1
    return sum(int(d) * c for d, c in freq.items())
```

**Skip zero (micro-optimization):**
```python
from collections import Counter

def digitFrequencyScore(n):
    return sum(int(d) * c for d, c in Counter(str(n)).items() if d != '0')
```

---

**Time:** O(d) where d ≤ 10 → effectively O(1) | **Space:** O(d)

**Pattern flag:** "Count something and sum a weighted aggregate" → `Counter` + sum over `.items()` with weighting baked into the generator. For digit problems, `str(n)` is the cleanest extraction in Python. "For each distinct X, sum f(X) × count(X)" is the universal Counter idiom — applies to digits, characters, item frequencies in any domain.
