# 3760. Maximum Substrings With Distinct Start

**Difficulty:** Medium  
**Pattern:** Hash Set / Counting Distinct  
**Date Solved:** 2026-05-19  
**Status:** ✅

---

## Understanding the Goal

You're splitting `s` into non-empty contiguous substrings. Constraint: every substring must start with a **different** character. Return the maximum number of pieces.

**Key insight (this is the whole problem):** The answer is just the **number of distinct characters in `s`**. Every character that appears in the string can become the start of its own substring — assign the first occurrence as that substring's start, and absorb the trailing characters somewhere. You can never split into more pieces than there are distinct starting characters, and you can always achieve exactly that many.

This problem looks like a DP/greedy split problem, but it collapses into a one-liner once you realize splits don't actually constrain each other — you have total freedom over substring lengths.

---

## LAYER 1: Line-by-Line Explanation

```python
class Solution:
    def maxDistinct(self, s: str) -> int:
        return len(set(s))    # Count distinct characters
```

**Why this works:**
- `set(s)` collects every unique character that appears in `s`
- `len(...)` gives the count of distinct characters
- Each distinct character contributes one valid substring; no character can contribute more than one (would violate the distinct-start rule)

**Why we don't need to construct the actual split:** The problem only asks for the count. But the construction is trivial: walk `s`, and whenever you hit a character you haven't used as a start yet, begin a new substring there.

---

## LAYER 2: Worked Examples

**Example 1: `s = "abab"` → 2**

Distinct chars in `"abab"`: `{'a', 'b'}` → 2 ✓

Concrete split: `"a"` + `"bab"` (starts: `a`, `b`).  
Alternative split: `"ab"` + `"ab"` ❌ (both start with `'a'` — invalid).  
Alternative split: `"aba"` + `"b"` ✓ (starts: `a`, `b`).

**Example 2: `s = "abcd"` → 4**

Distinct chars: `{'a','b','c','d'}` → 4 ✓

Split: `"a"` + `"b"` + `"c"` + `"d"`.

**Example 3: `s = "aaaa"` → 1**

Distinct chars: `{'a'}` → 1 ✓

Only one substring possible: `"aaaa"` itself.

**Walkthrough — why "more splits than distinct chars" is impossible:**

Suppose `s = "abab"` and you try to split into 3 pieces. Every piece must start with either `a` or `b` (those are the only characters). With 3 pieces and only 2 distinct letters, by pigeonhole two pieces share a starting letter → invalid. ❌

**Walkthrough — why we can always achieve exactly `len(set(s))`:**

Take `s = "aabbcc"`, distinct chars = `{a, b, c}` = 3.
- Find first `'a'` at index 0 → start piece 1 here
- Find first `'b'` not yet covered, at index 2 → end piece 1 at index 1, start piece 2 at index 2
- Find first `'c'`, at index 4 → end piece 2 at index 3, start piece 3 at index 4
- Piece 3 absorbs the rest

Split: `"aa"` + `"bb"` + `"cc"`. Starts: `a, b, c`. All distinct. ✓

**Edge case: `s = "z"`** → 1 distinct char → answer 1.

**Edge case: `s` = all 26 lowercase letters in some order, length 100,000** → 26 distinct → answer 26 (the cap, regardless of length).

---

## LAYER 3: Key Insights

| Aspect | Value |
|--------|-------|
| **Time** | O(n) — single pass to build the set |
| **Space** | O(σ) — σ = alphabet size = 26 (bounded constant) |
| **Approach** | Distinct character count |

**The trap this problem sets:** The phrasing "split into substrings" makes you reach for DP or interval thinking. But there's no interaction between splits — the only rule is "starting characters must be distinct". Splits don't constrain each other's lengths, so the question reduces to: *how many distinct first-characters can we possibly use?* Answer: every distinct character in `s`.

**Why the answer is exactly `len(set(s))`, never less:**  
For each distinct character `c`, place a "cut" right before its first occurrence in `s`. This gives a valid split (each piece starts with a different `c`). The first piece begins at index 0 and starts with `s[0]`; every other piece starts with a new character. Total pieces = number of distinct characters.

**Why the answer is exactly `len(set(s))`, never more:**  
Every piece has a starting character from the lowercase alphabet present in `s`. By the distinct-start rule, no two pieces share a start. So the number of pieces ≤ number of distinct characters present.

Combine both ⇒ equality.

**Constant-space variant:** Since the alphabet is fixed at 26, you could use a length-26 bool array instead of a set. Practically identical performance.

---

## LAYER 4: Interview Variations

• **Substrings must end with a distinct character instead of start:**  
  Symmetric — answer is still `len(set(s))`. Place cuts after the last occurrence of each character (reverse logic).

• **Distinct start AND distinct end:**  
  More restrictive. Now we need each piece's `(start_char, end_char)` to be unique-on-start AND unique-on-end. Answer ≤ min(distinct chars, distinct chars). Often still equals `len(set(s))` but not guaranteed — needs case-by-case construction.

• **Each substring must have distinct characters within itself (no internal repeats):**  
  Totally different problem — becomes a sliding-window / "longest substring without repeating characters" variant.

• **Maximize number of pieces where each piece is a palindrome:**  
  Classic DP problem (LC 132 Palindrome Partitioning II). The "no interaction between splits" trick doesn't apply.

• **At most k distinct starting characters allowed:**  
  Answer = `min(k, len(set(s)))`.

• **Each piece must have length ≥ L:**  
  Now splits DO constrain each other. Need DP or greedy with length tracking. Answer ≤ `min(len(set(s)), len(s) // L)`.

• **Unicode / arbitrary characters instead of lowercase letters:**  
  Same algorithm — `set(s)` still works, just space becomes O(unique chars) instead of O(26).

• **Return the actual split, not just the count:**  
  Track first-occurrence index of each character, sort those indices, use them as cut points:
  ```python
  def buildSplit(s):
      first = {}
      for i, c in enumerate(s):
          if c not in first:
              first[c] = i
      cuts = sorted(first.values())[1:]  # exclude 0
      pieces, prev = [], 0
      for c in cuts:
          pieces.append(s[prev:c])
          prev = c
      pieces.append(s[prev:])
      return pieces
  ```

• **Stream version (characters arrive one at a time, can't re-read):**  
  Maintain a running set; output its size at the end. O(1) per character.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — one-liner
class Solution:
    def maxDistinct(self, s: str) -> int:
        return len(set(s))
```

**Alternative — manual count (constant space, fixed alphabet):**
```python
def maxDistinct(s):
    seen = [False] * 26
    count = 0
    for c in s:
        idx = ord(c) - ord('a')
        if not seen[idx]:
            seen[idx] = True
            count += 1
    return count
```

**Alternative — using Counter (overkill but works):**
```python
from collections import Counter
def maxDistinct(s):
    return len(Counter(s))
```

---

**Time:** O(n) | **Space:** O(σ) where σ = alphabet size

**Pattern flag:** When a problem talks about "maximum splits" or "partitions" with a per-piece property that doesn't depend on lengths or positions, ask: *does the constraint reduce to counting something simple?* Often a deceptively-worded problem has a one-line answer hiding behind complex setup.
