# 3945. Digit Frequency Score

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | Math (collapse) / Counting |
| **Date** | 2026-08-11 |
| **Mode** | Reference Recall |
| **Link** | https://leetcode.com/problems/digit-frequency-score/ |

---

## 1. Input / Output
```
input  → n = 122
output → 5   (1·1 + 2·2 = 5)
```
Score = Σ d·freq(d) over distinct digits.

## 2. Data structure used
- `Counter` / a `[0..9]` frequency array — **or nothing** (see the collapse).

## 3. Why this approach works
**The definition is a disguise.** Digit `d` appearing `freq(d)` times contributes
`d + d + … + d` (freq(d) copies) = `d·freq(d)`. Summed over all distinct digits, that is simply
**every digit of n added up** — the frequency grouping cancels out. So the whole problem is:
```python
return sum(int(d) for d in str(n))
```
No Counter, no frequency array. This is a **mathematical collapse** — same family as the
archive's modular `sum % k`, Frobenius, and stars-and-bars: the elaborate counting definition
simplifies to something trivial. Hint 1 ("the answer is the sum of the digits") confirms it.

## 4. Time / space complexity
- O(digits) time, O(1) space (the collapsed version needs no frequency store).

---

## My solutions
```python
# collapsed (the real answer — grouping was never needed)
return sum(int(d) for d in str(n))

# 1 — Counter one-liner (what I submitted; correct but computes freqs it didn't need)
return sum(int(d) * c for d, c in Counter(str(n)).items())

# 3 — no Counter: peel digits with % and //, bucket into freq[0..9], sum d*freq[d]
```

## What tripped me up
Nothing broke — but I computed frequencies I never needed. Also: **Approach 4's `if d != '0'`
does nothing** — a zero digit contributes `0*count = 0` anyway, so filtering it is a
non-optimization. The lesson is to notice when the problem's structure collapses *before*
building machinery for it.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | Owed (trivial — the value is spotting the collapse) |
| **Where I got stuck** | — |

## Interview sentence
> The score is defined as Σ d·freq(d) over distinct digits, but algebraically that's just the
> sum of all digits — each digit contributes itself once per occurrence — so it's
> `sum(int(d) for d in str(n))` with no frequency count at all.
