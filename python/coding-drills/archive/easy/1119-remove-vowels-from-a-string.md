# 1119. Remove Vowels from a String

**Difficulty:** Easy
**Pattern:** String / Filter / Set Membership
**Date Solved:** 2026-06-05
**Status:** ✅

---

## Understanding the Goal

Remove all occurrences of `'a'`, `'e'`, `'i'`, `'o'`, `'u'`. Return what's left.

**Key insight:** Pure **filter** — keep chars NOT in the vowel set. Three Pythonic ways:
1. Generator + `str.join` — Pythonic
2. List comp + `str.join` — readable
3. `str.translate()` — fastest in practice

---

## LAYER 1: Line-by-Line Explanation

**Pythonic one-liner (preferred):**

```python
class Solution:
    def removeVowels(self, s: str) -> str:
        return ''.join(c for c in s if c not in 'aeiou')   # Keep non-vowels, join into string
```

**Why `''.join(generator)` not `+=`:**

`+=` in a loop is O(n²) — strings are immutable, every concat creates a new string. `''.join` over a generator is O(n) — pre-computes size and builds in one allocation.

**Set-based (faster lookup for larger filters):**

```python
class Solution:
    def removeVowels(self, s: str) -> str:
        vowels = set('aeiou')                              # O(1) lookup set
        return ''.join(c for c in s if c not in vowels)
```

**`str.translate()` — fastest:**

```python
class Solution:
    def removeVowels(self, s: str) -> str:
        # maketrans('', '', 'aeiou'): args are (replace_from, replace_to, delete_these)
        # Returns a translation table that DELETES every char in 'aeiou'
        return s.translate(str.maketrans('', '', 'aeiou'))
```

`str.maketrans()` three-argument form: `(chars_to_replace_from, chars_to_replace_to, chars_to_delete)`. We use only the delete argument.

**Regex (works, but slower due to compilation):**

```python
import re
class Solution:
    def removeVowels(self, s: str) -> str:
        return re.sub(r'[aeiou]', '', s)                   # Replace any vowel with empty string
```

**Explicit loop (beginner-friendly):**

```python
class Solution:
    def removeVowels(self, s: str) -> str:
        result = []                                        # Build list of kept chars
        for c in s:
            if c not in 'aeiou':                           # Membership check
                result.append(c)                           # Keep this char
        return ''.join(result)                             # Join into final string
```

---

## LAYER 2: Worked Examples

**Example 1: `"leetcodeisacommunityforcoders"` → `"ltcdscmmntyfrcdrs"`**

Walking each char:

| char | vowel? | kept? |
|------|--------|-------|
| l | no | ✅ |
| e | yes | ❌ |
| e | yes | ❌ |
| t | no | ✅ |
| c | no | ✅ |
| o | yes | ❌ |
| ... | ... | ... |

Final kept characters: `"ltcdscmmntyfrcdrs"` ✓

**Example 2: `"aeiou"` → `""`**

Every char is a vowel → empty string. ✓

**Edge cases:**
- No vowels: `"bcdfg"` → unchanged
- All vowels: `"aeiou"` → `""`
- Single consonant: `"b"` → `"b"`
- Constraints say `n ≥ 1`

**'y' is NOT a vowel here:**

The problem says "a, e, i, o, u" only. The 'y' in "community" (Example 1) survives — confirming this.

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Generator + join | O(n) | O(n) | **Pythonic default** |
| Set + generator | O(n) | O(n) | Habit for larger filters |
| `str.translate` | O(n) | O(n) | **Fastest on big inputs** |
| Regex | O(n) + compile | O(n) | Flexible, slower |
| `+=` in loop | O(n²) | O(n²) | ❌ AVOID |

**The classic Python trap — `+=` in a loop:**

```python
result = ""
for c in s:
    result += c        # ⚠️ Creates new string each time → O(n²) total
```

After n iterations, you've copied 1+2+...+n = O(n²) characters. Always use `list.append + ''.join` or generator + `''.join`.

**The filter pattern (one of the most reused):**

> Keep elements matching a predicate; discard the rest.

Three forms:
- Functional: `filter(predicate, iterable)`
- Comprehension: `[x for x in items if predicate(x)]`
- Imperative: `for ... if ... append`

For strings, also `str.translate()` (C-level fast).

**`str.translate()` deep cut:**

`str.maketrans()` has three forms:
- `maketrans({'a': 'X'})` — dict form
- `maketrans('ab', 'XY')` — replace
- `maketrans('', '', 'aeiou')` — delete

Memorize for translation/deletion tasks.

**Strings are immutable in Python:**

No in-place modification. Every "modification" creates a new string. This is why build-list-then-join exists.

---

## LAYER 4: Interview Variations

• **Remove ANY set of chars:** Generalize: `''.join(c for c in s if c not in remove_set)`.
• **Case-insensitive:** `vowels = set('aeiouAEIOU')` or `s.lower()` first.
• **Replace vowels with placeholder:** `''.join('*' if c in 'aeiou' else c for c in s)`.
• **Count vowels removed:** `sum(1 for c in s if c in 'aeiou')`.
• **Keep ONLY vowels:** Invert the condition.
• **Keep first occurrence only:** Track seen set.
• **Remove duplicates:** Hash set of seen chars.
• **List of strings:** `[remove_vowels(w) for w in words]`.
• **Streaming (char by char):** Yield non-vowels.
• **Two-pointer in-place (if mutable list):** Classic interview pattern, like LC 27.
• **Multiple char classes via regex:** `re.sub(r'[aeiouAEIOU0-9]', '', s)`.
• **Linguistic 'y' context-aware:** `(?<=[bcdfg])y(?=[bcdfg])` — much harder problem.
• **Reverse then remove:** `remove_vowels(s[::-1])`.
• **Count UNIQUE vowels:** `len(set(s) & set('aeiou'))`.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Pythonic generator
class Solution:
    def removeVowels(self, s: str) -> str:
        return ''.join(c for c in s if c not in 'aeiou')
```

**Fastest — translate:**
```python
def removeVowels(s):
    return s.translate(str.maketrans('', '', 'aeiou'))
```

**Set-based:**
```python
def removeVowels(s):
    vowels = set('aeiou')
    return ''.join(c for c in s if c not in vowels)
```

**Explicit loop:**
```python
def removeVowels(s):
    result = []
    for c in s:
        if c not in 'aeiou':
            result.append(c)
    return ''.join(result)
```

**Regex:**
```python
import re
def removeVowels(s):
    return re.sub(r'[aeiou]', '', s)
```

**Functional with filter:**
```python
def removeVowels(s):
    return ''.join(filter(lambda c: c not in 'aeiou', s))
```

---

**Time:** O(n) | **Space:** O(n)

**Pattern flag:** "Keep/discard elements based on membership" → **filter pattern.** For strings: `''.join(c for c in s if predicate(c))`. For long inputs, `str.translate()` is the C-level fast path. **Critical Python habit:** never use `+=` in a loop to build a string — it's O(n²). Always `list.append + ''.join` or generator + `''.join`.
