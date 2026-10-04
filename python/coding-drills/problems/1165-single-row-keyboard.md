# 1165. Single-Row Keyboard

| | |
|---|---|
| **Difficulty** | Easy |
| **Pattern** | Hash map |
| **Date** | 2026-07-19 |
| **Solve Mode** | `guided` |
| **Time to solve** | ~15 min (syntax debugging) |
| **Link** | https://leetcode.com/problems/single-row-keyboard/ |

---

## 1. Input / Output

**Input:**
```
keyboard = "abcdefghijklmnopqrstuvwxyz", word = "cba"
```

**Output:**
```
4
```
(0→2 for 'c' = 2, 2→1 for 'b' = 1, 1→0 for 'a' = 1; total 4)

**Constraints that matter:**
- `keyboard.length == 26`, each letter exactly once — a clean bijection, safe to map.
- `word` up to 10^4 — long enough that repeated linear scans of the keyboard would be
  wasteful. This is the signal to precompute.

---

## 2. Data structure used

- A **hash map** (dict) `char -> index`, built once from `keyboard`.
- Why not scan the keyboard per letter? The keyboard never changes, so pay O(26) once and
  make every lookup O(1). That trade — setup once, cheap repeated lookups — is the core
  hash-map move (same as Two Sum).

---

## 3. Why this approach works

The keyboard layout is fixed, so a letter's position is a constant I can look up instead of
recompute. I build the map once, then walk `word` carrying a running finger position:
for each char, look up its index, add the absolute distance from where the finger is, then
move the finger there. Absolute value because left and right jumps both cost positive time.

---

## 4. Time / space complexity

| | |
|---|---|
| **Time** | O(26 + m) ≈ O(m), m = len(word). One pass to build map, one over word, O(1) lookups |
| **Space** | O(26) = O(1) — map never exceeds 26 entries regardless of word length |
| **Naive approach** | O(26 × m) — scanning keyboard per letter; works but is what the map replaces |

---

## My solution

```python
class Solution:
    def calculateTime(self, keyboard: str, word: str) -> int:
        char_to_index = {char: index for index, char in enumerate(keyboard)}

        total_time = 0
        current_position = 0

        for char in word:
            target_position = char_to_index[char]
            total_time += abs(target_position - current_position)
            current_position = target_position

        return total_time
```

---

## What went wrong first

The **algorithm was correct on the first attempt** — the loop, the accumulator, and the
finger-position carry were all right. Two **syntax** errors stopped it running:

1. **Dict comprehension written as a set.** Wrote `{char == index for ...}` — `==` is
   comparison (produces `True`/`False`), and with no colon the `{}` is a *set*, not a dict.
   Fix: `{char: index for index, char in enumerate(keyboard)}`. The colon is what makes it a
   dict; the key must be `char` (what I look up by), value `index` (what I want back), even
   though `enumerate` hands them back as `(index, char)`.

2. **`abs[...]` instead of `abs(...)`.** `abs` is a function → parentheses. Square brackets
   mean "index into"; they can't call.

**The transferable lesson — brackets are not interchangeable:**

| Bracket | Means | Example |
|---|---|---|
| `( )` | call / group | `abs(x)`, `enumerate(kb)` |
| `[ ]` | index / subscript | `d[char]` |
| `{k: v}` | dict | `{char: index}` |
| `{ }` no colon | **set** | `{char}` ← the trap |

---

## Cold variant rep

| | |
|---|---|
| **Variant problem** | Alphabetical keyboard, finger starts at 'a' and must return to 'a'; use `ord(c)-ord('a')` instead of a map |
| **Date** | — |
| **Solved unaided?** | **OPEN — not yet attempted** |
| **Notes** | Tests the return-home extra jump and computing position with `ord()` instead of a dict. Required before this counts as closed. |

---

## Interview sentence

> When positions are fixed and I'll look them up many times, I precompute a char→index dict
> once so each lookup is O(1) — the same trade behind Two Sum. My mistake here wasn't the
> algorithm, it was writing a set comprehension where I meant a dict, which is exactly the
> fluency gap I'm drilling.
