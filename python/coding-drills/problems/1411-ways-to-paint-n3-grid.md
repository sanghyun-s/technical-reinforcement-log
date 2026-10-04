# 1411. Number of Ways to Paint N × 3 Grid

| | |
|---|---|
| **Difficulty** | Hard |
| **Family** | DP (state compression) / matrix exponentiation |
| **Date** | 2026-08-15 |
| **Mode** | Reference Recall — A1/A2 solid; A3 (matrix expo) **conceptual** |
| **Link** | https://leetcode.com/problems/number-of-ways-to-paint-n-3-grid/ |

---

## 1. Problem
Paint an n×3 grid with 3 colors, no two adjacent cells equal. Count colorings mod 1e9+7.

## 2. The core insight — state compression (the Hard part)
A valid row has **12 patterns** (3×2×2). But they collapse to **two classes** by shape:
- **ABA** — ends share a color (Red-Yellow-Red). 6 of them.
- **ABC** — all three distinct. 6 of them.

Why 12 → 2: what constrains the *next* row is the row's **shape**, not its specific colors. So
the count depends only on ABA-vs-ABC. Transitions (the derivation): an **ABA** row is followed by
**3 ABA + 2 ABC**; an **ABC** row by **2 ABA + 2 ABC**.

## 3. The optimization ladder
- **A2 — full-state DP, O(144n):** all 12 patterns + a 12×12 compatibility matrix. Most
  obviously correct, needs no symmetry insight — **write this first in an interview**, derive A1 from it.
- **A1 — 2-state DP, O(n):** the 12→2 collapse. `aba, abc = 3*aba+2*abc, 2*aba+2*abc` per row,
  `% MOD` every step. ~72× less work than A2, same linear class. **The submit.**
- **A3 — matrix exponentiation, O(log n):** the transition is a *linear recurrence*, so it's a
  matrix multiply `[[3,2],[2,2]]`; applying it n−1 times = that matrix to the (n−1) power, done by
  square-and-multiply in O(log n).

## 4. The judgment call (my note, made precise)
**A3 is asymptotically best but over-engineering here.** At n ≤ 5000, O(n) is 5000 trivial ops;
O(log n) is ~13 heavier 2×2 multiplies + overhead — so the **O(n) version likely wins on
wall-clock** and is far simpler. Matrix expo only pulls ahead at astronomical n (10⁹–10¹⁸).
Same lesson as digit DP (3751) and √n decomposition: *asymptotically-best isn't always
constraint-appropriate — pick the simplest tool the constraint allows, unless a "Part II" scales n up.*

## 5. Correctness detail
`% MOD` at **every step**, not just the end — keeps the running values bounded (overflow-safe;
in fixed-width-int languages, wrong otherwise).

---

## My solutions
```python
# A1 — 2-state DP (submit)
MOD = 10**9 + 7
aba, abc = 6, 6
for _ in range(n-1):
    aba, abc = (3*aba + 2*abc) % MOD, (2*aba + 2*abc) % MOD
return (aba + abc) % MOD
# A2 — 12-pattern full-state DP, O(144n)
# A3 — matrix power of [[3,2],[2,2]] via square-and-multiply, O(log n)  [conceptual]
```

## Interview sentence
> 12 valid row patterns collapse to two classes — ABA and ABC — because only the row's shape
> constrains the next row. Full-state DP is O(144n); compressing to two states is O(n); and since
> the transition is a linear recurrence, matrix exponentiation gets O(log n). For n ≤ 5000 I'd
> submit the O(n) two-state version — simplest and fast enough — and only reach for matrix expo
> if n could be billions.
