# 2942. Find Words Containing Character

**Difficulty:** Easy
**Pattern:** String / Linear Scan / Indexed Filter
**Date Solved:** 2026-06-05
**Status:** ✅

---

## Understanding the Goal

For each word in `words`, check if it contains the character `x`. Return the **indices** (not the words themselves) of all matching words.

**Key insight:** Two micro-decisions:
1. **Membership check** — Python's `x in words[i]` is O(word length) substring scan
2. **Index tracking** — use `enumerate` (cleaner) or `range(n)` (editorial style)

The editorial's "two nested loops" hint refers to: outer over words, inner over chars. Python's `in` operator does the inner loop for you.

---

## LAYER 1: Line-by-Line Explanation

**Editorial — explicit index loop:**

```python
class Solution:
    def findWordsContaining(self, words: List[str], x: str) -> List[int]:
        res = []                                # Accumulator for matching indices
        n = len(words)                          # Cache length (minor; not needed in Python)
        
        for i in range(n):                      # Iterate by index 0..n-1
            if x in words[i]:                   # Substring check: True if x appears
                res.append(i)                   # Save the INDEX, not the word
        
        return res
```

**Why `x in words[i]` works:** For a single-char `x`, Python scans the string left-to-right, returns True on first match, short-circuits.

**Cleaner Pythonic — `enumerate`:**

```python
class Solution:
    def findWordsContaining(self, words: List[str], x: str) -> List[int]:
        res = []
        for i, word in enumerate(words):        # i = index, word = words[i] — both at once
            if x in word:                       # Same substring check
                res.append(i)
        return res
```

`enumerate(iterable)` yields `(index, value)` pairs — standard whenever you need both.

**One-liner with list comprehension:**

```python
class Solution:
    def findWordsContaining(self, words: List[str], x: str) -> List[int]:
        # For each (i, word) pair, keep i IF x is in word
        return [i for i, word in enumerate(words) if x in word]
```

Decoding the structure:

```
[ i               for i, word in enumerate(words)   if x in word ]
  ^               ^                                  ^
  what to keep    how to iterate                     filter
```

---

## LAYER 2: Worked Examples

**Example 1: `words = ["leet", "code"]`, `x = "e"` → `[0, 1]`**

| i | word | `'e' in word`? | action |
|---|------|----------------|--------|
| 0 | "leet" | True | append 0 |
| 1 | "code" | True | append 1 |

Result: `[0, 1]` ✓

**Example 2: `words = ["abc", "bcd", "aaaa", "cbc"]`, `x = "a"` → `[0, 2]`**

| i | word | `'a' in word`? | action |
|---|------|----------------|--------|
| 0 | "abc" | True | append 0 |
| 1 | "bcd" | False | skip |
| 2 | "aaaa" | True | append 2 |
| 3 | "cbc" | False | skip |

Result: `[0, 2]` ✓

**Example 3: `words = [...]`, `x = "z"` → `[]`**

No word contains 'z' → empty list. ✓

**Walking through the list comp on Example 2:**

```
enumerate yields: (0, "abc"), (1, "bcd"), (2, "aaaa"), (3, "cbc")
For each (i, word):
  (0, "abc")  → 'a' in "abc"  → True  → yield 0
  (1, "bcd")  → 'a' in "bcd"  → False → skip
  (2, "aaaa") → 'a' in "aaaa" → True  → yield 2
  (3, "cbc")  → 'a' in "cbc"  → False → skip
Result: [0, 2]
```

**Edge cases:**
- Single word, contains x: `["abc"], x="a"` → `[0]`
- All match: all indices returned
- None match: `[]` (empty, NOT None)
- x appears multiple times in one word: index added ONCE (no double-count)
- Constraints guarantee n ≥ 1

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Loop + `range(n)` (editorial) | O(n × w) | O(k) | Slightly clunky |
| Loop + `enumerate` | O(n × w) | O(k) | **Cleaner** |
| List comprehension | O(n × w) | O(k) | **Most Pythonic** |

n = num words, w = avg word length, k = matches.

**What this "Easy" tests:**

Two Python fundamentals:
1. **Substring membership** — knowing `x in word` exists
2. **Indexed iteration** — knowing `enumerate` is the right tool when you need both index AND value

Beginners write:
```python
for i in range(len(words)):
    for j in range(len(words[i])):
        if words[i][j] == x:
            res.append(i)
            break
```
That's the literal "two nested loops" hint — but Python's `in` operator hides the inner loop and short-circuits.

**The `enumerate` decision tree:**

| Need... | Use |
|---------|-----|
| Just values | `for v in iterable` |
| Just indices | `for i in range(len(iterable))` |
| Both | `for i, v in enumerate(iterable)` |
| Both, starting at N | `for i, v in enumerate(iterable, start=N)` |

**Substring `in` variants:**

| Expression | What it does |
|------------|--------------|
| `'a' in word` | Linear scan; True if 'a' appears |
| `word.find('a')` | Returns first index (-1 if not) |
| `word.index('a')` | Same, but raises ValueError if not found |
| `word.count('a') > 0` | Wasteful — counts ALL first |

**Return index vs value:**

The problem asks for **indices**, not words. Always re-read output spec — "indices" and "elements" are different.

---

## LAYER 4: Interview Variations

• **Return actual words:** `[w for w in words if x in w]`. Drop enumerate.
• **Return (index, word) pairs:** `[(i, w) for i, w in enumerate(words) if x in w]`.
• **Contains ALL chars in x:** `[i for i, w in enumerate(words) if all(c in w for c in x)]`.
• **Contains ANY char in x:** `any(c in w for c in x)`.
• **Case-insensitive:** Lowercase both sides.
• **Count occurrences instead of "exists":** `[(i, w.count(x)) ...]`.
• **Multi-character substring:** Same `in` works; Python's substring search is O(n).
• **Many queries (same words):** Pre-compute `char → indices` map for O(1) per query.
• **Bitmask version (long words):** 26-bit mask per word; `(mask >> bit) & 1`.
• **Find NOT containing x:** Flip to `if x not in word`.
• **First/last char check:** `w.startswith(x)` / `w.endswith(x)`.
• **Only unique char:** `if set(w) == {x}`.
• **Streaming words:** Maintain running list; emit index on match.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — list comprehension
class Solution:
    def findWordsContaining(self, words: List[str], x: str) -> List[int]:
        return [i for i, word in enumerate(words) if x in word]
```

**Loop + enumerate:**
```python
def findWordsContaining(words, x):
    res = []
    for i, word in enumerate(words):
        if x in word:
            res.append(i)
    return res
```

**Editorial form (range-based):**
```python
def findWordsContaining(words, x):
    res = []
    n = len(words)
    for i in range(n):
        if x in words[i]:
            res.append(i)
    return res
```

**Functional with filter:**
```python
def findWordsContaining(words, x):
    return [i for i, w in filter(lambda iw: x in iw[1], enumerate(words))]
```

---

**Time:** O(n × w) | **Space:** O(k) for result

**Pattern flag:** "Iterate a collection and collect INDICES satisfying a predicate" → `[i for i, x in enumerate(items) if predicate(x)]`. The **indexed filter** pattern — distinct from a plain filter that returns values. Memorize: `enumerate` for both index and value; `range(len(...))` for indices only; bare `for x in ...` for values only.
