# 3541. Find Most Frequent Vowel and Consonant

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | Hash / Counting |
| **Date** | 2026-09-18 |
| **Mode** | Reference Recall (A1 rewritten/submitted, A2–A4 hand-typed from archive) |
| **Link** | https://leetcode.com/problems/find-most-frequent-vowel-and-consonant/ |

---

## 1. Input / Output
```
input  → s = "successes"
output → 6   (max vowel freq 2 + max consonant freq 4)
```
Sum of (max vowel frequency) + (max consonant frequency).

## 2. The idea + the detail that matters
Count once (`Counter(s)`), then take the max frequency among vowels and among consonants
separately, and sum. **The correctness core is the empty-group guard:** if there are no vowels
(or no consonants), `max()` over an empty sequence **raises ValueError** — a crash, not 0. Two
valid fixes: `max(..., default=0)` (A1/A3) or a zero-initialised accumulator that only updates
upward (A2/A4). The problem's note ("consider their frequency as 0") points straight at this.

## 3. Complexity
O(n) — read every char to count; then a pass over ≤26 distinct keys. Set membership is O(1).

---

## My solutions
```python
# 3 — idiomatic (best): set membership + generator max with default
mp = Counter(s)
vowels = set("aeiou")                        # `in` on a set is O(1); on a string it's O(k)
vowel_max     = max((c for ch, c in mp.items() if ch in vowels), default=0)
consonant_max = max((c for ch, c in mp.items() if ch not in vowels), default=0)
return vowel_max + consonant_max

# 1 — same, but `ch in "aeiou"` (string scan) — works, set is the scaling habit
# 2 — explicit single loop over mp.items(), zero-init maxes (the other empty-guard strategy)
# 4 — fixed 26-element freq array + vowel index set (counting-sort flavour, bounded alphabet)
```

## What tripped me up
Nothing broke. Two fluency notes: (1) `default=0` / zero-init is the empty-group guard — any
"max over a filtered group" needs it; (2) `set("aeiou")` membership is O(1) vs a string's O(k) —
irrelevant at n=5 but the correct instinct.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | Owed |
| **Where I got stuck** | — |

## Interview sentence
> Count with a Counter, take the max frequency among vowels and among consonants separately, and
> sum — with `default=0` on `max` so an empty group returns 0 instead of crashing, and set
> membership for O(1) checks. O(n).
