# 1255. Maximum Score Words Formed by Letters

| | |
|---|---|
| **Difficulty** | Hard |
| **Family** | Bitmask subset enumeration / backtracking |
| **Date** | 2026-08-15 |
| **Mode** | Reference Recall (A1 submitted, A2/A3 hand-typed from archive) |
| **Link** | https://leetcode.com/problems/maximum-score-words-formed-by-letters/ |

---

## 1. Problem
Pick a subset of `words` (each used ≤ once) that fits the `letters` budget; maximize total score.

## 2. The unlock — read the constraint
`words.length <= 14` → `2^14 = 16,384` subsets. Tiny. **"N ≤ ~20 and choose a subset" = exhaust
every subset.** The problem looks like it needs cleverness; the small N means brute force over
all subsets is the intended answer (Hint 1 says so). Same "constraint picks the tool" judgment —
here it says *exponential is fine*.

## 3. Two paradigms, same 2^W search
- **Bitmask enumeration (A1, A2):** integer `mask` 0..2^W−1 encodes a subset; bit i = word i in.
  `mask & (1<<i)` tests membership. Tally letter demand, check ≤ budget, score, track max.
- **Take-or-skip recursion (A3):** `dfs(i, budget)` = best from word i on; branch **skip**
  (`dfs(i+1, budget)`) vs **take if it fits** (`score + dfs(i+1, new_budget)`), return max. The
  binary decision tree generates the *same* 2^W subsets — iterative vs recursive clothing.

## 4. Correctness core
A subset is valid iff **for every letter, demand ≤ budget** (per-letter, not total). Example 3
(`leetcode` needs two e's, one available → 0) catches solutions that check total letters only.

## 5. Complexity
All O(2^W · L). **A2 pre-computes each word's Counter + score once** outside the loop — A1
re-walks characters inside every subset (redundant). Same class, but A2 = "don't recompute in the
loop what you can compute once" (the 538/merge-sort lesson as constant-factor hygiene).

---

## My solutions
```python
# A1/A2 — bitmask: for mask in range(1<<W): include word i if mask & (1<<i)
# A2 hoists word_counts = [Counter(w) ...] and word_scores out of the loop
# A3 — recursion:
def dfs(i, budget):
    if i == len(words): return 0
    skip = dfs(i+1, budget)
    ok, new_budget = can_take(words[i], budget)
    take = word_score(words[i]) + dfs(i+1, new_budget) if ok else 0
    return max(skip, take)
```

## Notes
- **Bitmask vs backtracking:** bitmask is cleaner when you want *every* subset with no pruning;
  recursion naturally supports pruning + carries the shrinking budget down so it doesn't re-tally.
- Bitmask trick: the integer IS the loop over subsets; `& (1<<i)` reads bit i.

## Interview sentence
> words ≤ 14 means 2^14 subsets — small enough to enumerate exhaustively. Two ways: a bitmask
> loop where each integer encodes which words are in, or take-or-skip recursion branching on each
> word — same 2^W search, iterative vs recursive. I pre-compute each word's letter-count and
> score once, and a subset is valid only if demand ≤ budget for every letter.
