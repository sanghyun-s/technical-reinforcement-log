# 3751. Total Waviness of Numbers in Range I

| | |
|---|---|
| **Difficulty** | Medium |
| **Family** | Digit DP (+ brute force) |
| **Date** | 2026-07-31 |
| **Mode** | Reference Recall — **Shaky→Stuck** (digit DP passed on 7th attempt) |
| **Link** | https://leetcode.com/problems/total-waviness-of-numbers-in-range-i/ |

---

## 1. Input / Output
```
input  → num1 = 120, num2 = 130
output → 3   (120, 121, 130 each have one peak/valley)
```
Waviness = count of strict peaks + strict valleys among the *interior* digits. Sum over the range.

## 2. Data structure used
- Brute force: string scan per number. Digit DP: memoized recursion over digit positions.

## 3. Why this approach works
**Brute force (Approaches 1–2):** for each number, slide a 3-digit window; a middle digit is a
peak if `a<b>c`, a valley if `a>b<c`. Sum over `[num1, num2]`. O(range · digits) — fine because
`num2 ≤ 10⁵` (≤100k numbers).

**Digit DP (Approach 3):** counts waviness *combinatorially* over digit structure instead of
visiting each number, via the range trick `solve(num2) − solve(num1−1)`. State carries the two
previous digits (to test peak/valley), an `isLimit` tight-bound flag, and an `isLeading`
zero flag; returns `(count, waviness)`, memoized. O(digits · states) — independent of range width.

## 4. Time / space complexity
- Brute: O(range · digits).
- Digit DP: ~O(digits · states) ≈ constant here.

---

## My solutions
```python
# 2 — brute force, the clean idiom (submit at this constraint)
def waviness(n):
    s = str(n)
    if len(s) < 3: return 0
    return sum(1 for a,b,c in zip(s, s[1:], s[2:])
                 if (a<b and b>c) or (a>b and b<c))
return sum(waviness(n) for n in range(num1, num2+1))
```
Approach 3 (digit DP) kept in the repo for the *technique*, not as the answer here — see the
full 5-state `dfs(pos, prev, curr, isLimit, isLeading)` in the session notes.

## What tripped me up
Digit DP took **7 attempts** — leading zeros, the tight-bound flag, and *when* to check the
peak/valley are all fiddly. Honest grade: near-stuck. The key judgment, though:
**at 10⁵ the digit DP is over-engineering** — brute force is correct, 6 lines, and explainable.
Digit DP only becomes necessary at ~10¹⁸ (the "Range II" the title hints at), where brute force
is hopeless. Mirror image of 2974/349: there the fancy tool was unneeded and simple won; here
the fancy tool is *also* unneeded for this constraint, but it's the one that scales.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | **Owed — conceptual, not reproduction** |
| **Where I got stuck** | Digit DP state. Better rep: re-explain the 5 state pieces + why each exists, without retyping |

## Interview sentence
> At 10⁵ I'd brute-force it — slide a 3-digit window over each number, O(range·digits), simple
> and correct. If the range were up to 10¹⁸, brute force dies and you need digit DP: count
> combinatorially over digit positions with the solve(hi) − solve(lo−1) trick.
