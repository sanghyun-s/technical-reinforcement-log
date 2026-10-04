# 1255. Maximum Score Words Formed by Letters

**Difficulty:** Hard ⭐
**Pattern:** Dynamic Programming / Bitmask Enumeration / Subset Sum (knapsack variant)
**Date Solved:** 2026-06-09
**Status:** ✅

---

## Understanding the Goal

Given a list of words, a letter budget, and per-character scores: pick a **subset of words** whose combined letter demand fits within budget. Maximize total score.

**Key insight — the constraint IS the algorithm:**

`words.length ≤ 14`. **2¹⁴ = 16,384 subsets** — small enough to enumerate ALL. This is **bitmask enumeration** (you've drilled this in LC 1863 Subset XOR Sum on Day 8).

For each of 2^W subsets:
1. Combine letter demand of all chosen words
2. Check: demand fits within letter budget?
3. If yes: compute score; track max

The hint **explicitly endorses this**: "words.length is small. This means you can iterate over every subset of words (2^N)."

**Second encounter with subset bitmask enumeration.** Same template as LC 1863.

---

## LAYER 1: Line-by-Line Explanation

### Editorial — bitmask enumeration

```python
class Solution:
    def maxScoreWords(self, words: List[str], letters: List[str], score: List[int]) -> int:
        W = len(words)
        
        # Step 1: letter budget (how many of each char available)
        # freq[0] = 'a' count, freq[1] = 'b', etc.
        freq = [0] * 26
        for c in letters:
            freq[ord(c) - ord('a')] += 1
        
        # Helper: given subset's letter demand, return score (or 0 if infeasible)
        def subset_score(subset_letters):
            total_score = 0
            for c in range(26):
                total_score += subset_letters[c] * score[c]
                # Reject if demand > budget for ANY letter
                if subset_letters[c] > freq[c]:
                    return 0                # Infeasible → score 0 (won't beat max)
            return total_score
        
        max_score = 0
        
        # Step 2: try every subset, encoded as bitmask
        # mask = 0b0000 → no words; mask = 0b1111 → all words
        for mask in range(1 << W):          # 1 << W = 2^W
            subset_letters = [0] * 26       # Reset for this subset
            
            # Step 3: count letters used by words in this subset
            for i in range(W):
                # Bit i set → word i is in subset
                if (mask & (1 << i)) > 0:
                    for ch in words[i]:
                        subset_letters[ord(ch) - ord('a')] += 1
            
            # Step 4: score; update max
            max_score = max(max_score, subset_score(subset_letters))
        
        return max_score
```

**Decoding the bitmask:**
- `1 << W` = 2^W. For W=4, this is 16. Mask ranges 0 to 15.
- `mask = 0b1011 = 11` means: include words 0, 1, 3 (skip 2)
- `mask & (1 << i)` extracts bit i; nonzero → "word i is included"

**The canonical "enumerate all subsets" idiom** in bit manipulation.

**Why "score = 0 for infeasible" works:**

Empty subset has score 0 (no words). Any infeasible subset is "no worse than empty." `max_score` starts at 0, so infeasible can't displace valid. **Sentinel value trick.**

### Optimized — precompute per-word counts and scores

```python
from collections import Counter

class Solution:
    def maxScoreWords(self, words, letters, score):
        W = len(words)
        budget = Counter(letters)
        
        # Pre-compute (avoid recomputing inside 2^W loop)
        word_counts = [Counter(w) for w in words]
        word_scores = [sum(score[ord(c) - ord('a')] * cnt for c, cnt in wc.items())
                       for wc in word_counts]
        
        best = 0
        for mask in range(1 << W):
            demand = Counter()
            total = 0
            for i in range(W):
                if mask & (1 << i):
                    demand += word_counts[i]
                    total += word_scores[i]
            
            if all(demand[c] <= budget[c] for c in demand):
                best = max(best, total)
        
        return best
```

**Pre-computation gain:** Each word's letter count and score computed ONCE, not 2^W times.

### DFS take-or-skip (knapsack form)

```python
class Solution:
    def maxScoreWords(self, words, letters, score):
        budget = [0] * 26
        for c in letters:
            budget[ord(c) - ord('a')] += 1
        
        def word_score(word):
            return sum(score[ord(c) - ord('a')] for c in word)
        
        def can_take(word, budget):
            need = [0] * 26
            for c in word:
                need[ord(c) - ord('a')] += 1
            for i in range(26):
                if need[i] > budget[i]:
                    return False, None
            return True, [b - n for b, n in zip(budget, need)]
        
        def dfs(i, budget):
            if i == len(words):
                return 0
            skip = dfs(i + 1, budget)
            ok, new_budget = can_take(words[i], budget)
            take = word_score(words[i]) + dfs(i + 1, new_budget) if ok else 0
            return max(skip, take)
        
        return dfs(0, budget)
```

**Take-or-skip recursion** — classic 0/1 knapsack shape per word. For W ≤ 14, equivalent to bitmask. Bitmask is preferred here (flat, no recursion).

---

## LAYER 2: Worked Examples

### Example 1: `words = ["dog","cat","dad","good"]`, letters → `{a:2, c:1, d:3, g:1, o:2}`

Score: a=1, c=9, d=5, g=3, o=2

W = 4 → 16 subsets.

**Per-word:**

| word | demand | score |
|------|--------|-------|
| dog | d:1, o:1, g:1 | 10 |
| cat | c:1, a:1, t:1 | 10 |
| dad | d:2, a:1 | 11 |
| good | g:1, o:2, d:1 | 12 |

**Best feasible:**

| subset | demand | feasible? | score |
|--------|--------|-----------|-------|
| {dog, cat} | d:1, o:1, g:1, c:1, a:1 | ✓ | 20 |
| **{dad, good}** | **d:3, a:1, g:1, o:2** | ✓ (d:3≤3) | **23** |
| {dog, dad} | d:3, o:1, g:1, a:1 | ✓ | 21 |
| {dog, cat, dad} | t needed, but no t | ✗ | 0 |
| {all four} | d:4 > 3 | ✗ | 0 |

Max: **23** ✓

### Example 2: `words = ["xxxz","ax","bx","cx"]`, letters → `{a:1, b:1, c:1, x:3, z:1}`

Score: a=4, b=4, c=4, x=5, z=10

- {xxxz}: score 25
- {ax, bx, cx}: x:3, a:1, b:1, c:1 — uses 3 x's; **9 × 3 = 27** ✓
- {xxxz, ax}: x:4 > 3 → infeasible

Max: **27** ✓

### Example 3: `words = ["leetcode"]`, letters → `{c:1, d:1, e:1, l:1, o:1, t:1}`

Demand: e:3 > budget e:1 → **infeasible**. Only empty subset feasible → **0** ✓

### Bitmask trace for Example 1 (selected masks)

```
mask = 0b1100 (12): {dad, good} → d:3, a:1, g:1, o:2 → score 23 ← MAX
mask = 0b1101 (13): {dog, dad, good} → d:4 > 3 → infeasible
mask = 0b1110 (14): {cat, dad, good} → no 't' in budget → infeasible
mask = 0b1111 (15): all four → infeasible
```

### Edge cases

- **No letters:** Only empty feasible → 0
- **No words:** W=0, loop runs once (mask=0) → 0
- **Word needs missing letter:** Never in feasible subset
- **All scores 0:** Any subset → 0
- **W = 14:** 16,384 subsets × ~14 words × 15 chars = ~3.4M ops — fine

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **Bitmask enumeration (editorial)** | O(2^W × (W × L + 26)) | O(26) | **Standard** |
| Bitmask + precomputed | O(2^W × (W + 26)) | O(W × 26) | Faster constant |
| DFS take-or-skip | O(2^W) | O(W) recursion | Allows pruning |
| Brute permutations | O(W!) | — | Wrong tree |

For W=14: 2^W = 16384. All instant.

**"Constraint signals algorithm" — the meta-skill:**

| Constraint | Implied technique |
|------------|-------------------|
| n ≤ 12 | Brute permutations (n!) |
| n ≤ 20 | **Subset bitmask (2^n)** |
| n ≤ 100 | O(n³) DP |
| n ≤ 10⁴ | O(n²) DP or O(n log n) |
| n ≤ 10⁶ | O(n) or O(n log n) |
| n ≤ 10⁹ | O(log n) or math |

`words.length ≤ 14` SCREAMS subset bitmask. Hint confirms. **Constraint-recognition is one of the most useful interview meta-skills.**

**The bitmask enumeration template (memorize):**

```python
for mask in range(1 << n):              # All 2^n subsets
    selected = []
    for i in range(n):
        if mask & (1 << i):             # Bit i set → item i included
            selected.append(items[i])
    # Process `selected`
```

You've seen this in:
- **LC 1863 (Day 8)** Subset XOR Totals — XOR over each subset
- **LC 1255 (this)** — feasibility check + max score over each subset

Loop body changes; iteration framework identical.

**Why this is "DP" family:**

Strictly, it's **exhaustive enumeration**. But it sits in DP family because:
1. **Take-or-skip recursion** IS classic 0/1 knapsack DP
2. Bitmask iteration = flattening that recursion
3. **Letter budget constraint** mirrors weight constraints in knapsack

**Connection to 0/1 knapsack:**

| Knapsack | This problem |
|----------|--------------|
| n items | W words |
| Weight per item | Letter demand per word |
| Knapsack capacity | Letter budget |
| Value per item | Word score |
| Maximize value, weight ≤ capacity | Maximize score, demand ≤ budget |

**Twist:** instead of scalar weight, **26-dimensional vector constraint** (one per letter). Classic DP-tabulation knapsack (with `dp[capacity]`) doesn't directly apply — would need 26-dim table. Bitmask enumeration sidesteps this.

**Pre-computing word counts/scores (optimization):**

Computing `Counter(word)` inside the main loop is wasteful — doesn't depend on mask. Precompute once. For W = L = 15, modest speedup but cleaner code.

---

## LAYER 4: Interview Variations

• **Maximum number of words:** Replace score with `+1` per word.
• **Letters reusable (no budget):** Take all words.
• **Each letter has a cost; minimize subject to "at least K words":** Same framework.
• **Words can be reused:** Unbounded knapsack — bitmask doesn't apply.
• **W up to 30:** 2^30 = 1B — too many. Need smarter DP.
• **Find actual subset, not just score:** Track best mask alongside score.
• **K-th best subset:** Sort all feasible by score.
• **Top K disjoint subsets:** Repeated enumeration.
• **Most "valuable" letter:** Post-process best subset.
• **All optimal subsets:** Collect every subset achieving max_score.
• **Lexicographically smallest optimal:** Tiebreak by mask value.
• **Online (words arrive):** Re-run after each addition.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Editorial bitmask enumeration
class Solution:
    def maxScoreWords(self, words: List[str], letters: List[str], score: List[int]) -> int:
        W = len(words)
        freq = [0] * 26
        for c in letters:
            freq[ord(c) - ord('a')] += 1
        
        def subset_score(subset_letters):
            total = 0
            for c in range(26):
                total += subset_letters[c] * score[c]
                if subset_letters[c] > freq[c]:
                    return 0
            return total
        
        max_score = 0
        for mask in range(1 << W):
            subset_letters = [0] * 26
            for i in range(W):
                if mask & (1 << i):
                    for ch in words[i]:
                        subset_letters[ord(ch) - ord('a')] += 1
            max_score = max(max_score, subset_score(subset_letters))
        return max_score
```

**Optimized — precompute:**
```python
from collections import Counter

class Solution:
    def maxScoreWords(self, words, letters, score):
        W = len(words)
        budget = Counter(letters)
        word_counts = [Counter(w) for w in words]
        word_scores = [sum(score[ord(c) - 97] * cnt for c, cnt in wc.items())
                       for wc in word_counts]
        
        best = 0
        for mask in range(1 << W):
            demand = Counter()
            total = 0
            for i in range(W):
                if mask & (1 << i):
                    demand += word_counts[i]
                    total += word_scores[i]
            if all(demand[c] <= budget[c] for c in demand):
                best = max(best, total)
        return best
```

**DFS take-or-skip:**
```python
class Solution:
    def maxScoreWords(self, words, letters, score):
        budget = [0] * 26
        for c in letters:
            budget[ord(c) - 97] += 1
        
        def dfs(i, budget):
            if i == len(words):
                return 0
            skip = dfs(i + 1, budget)
            need = [0] * 26
            for c in words[i]:
                need[ord(c) - 97] += 1
            if all(need[j] <= budget[j] for j in range(26)):
                word_sc = sum(need[j] * score[j] for j in range(26))
                new_budget = [b - n for b, n in zip(budget, need)]
                take = word_sc + dfs(i + 1, new_budget)
                return max(skip, take)
            return skip
        
        return dfs(0, budget)
```

**Functional with itertools:**
```python
from itertools import chain, combinations
from collections import Counter

class Solution:
    def maxScoreWords(self, words, letters, score):
        budget = Counter(letters)
        
        def feasible_and_score(subset):
            demand = Counter(''.join(subset))
            if all(demand[c] <= budget[c] for c in demand):
                return sum(score[ord(c) - 97] * cnt for c, cnt in demand.items())
            return 0
        
        all_subsets = chain.from_iterable(
            combinations(words, r) for r in range(len(words) + 1)
        )
        return max(feasible_and_score(s) for s in all_subsets)
```

---

**Time:** O(2^W × (W × L + 26)) ≈ 3.7M ops for W=L=15 | **Space:** O(26)

**Pattern flag:** "Small N (≤ 20), pick subset to optimize" → **bitmask enumeration**. `N ≤ 14` is explicit signal. Iterate `mask in range(1 << N)`; check bit i to decide inclusion. Each subset independent.

Generalizes **multi-dimensional 0/1 knapsack** — instead of single weight constraint, per-letter budget vector. Brute-force subset check feasible because N small; classic DP knapsack would need 26-dim table.

Two settings for subset bitmask:
- **LC 1863 (Day 8):** sum over all subsets of XOR — no constraint
- **LC 1255 (this):** maximize subject to vector constraint — enumerate + filter

⭐ **First algorithmic Hard since LC 3368 (SQL Recursive CTE).**
