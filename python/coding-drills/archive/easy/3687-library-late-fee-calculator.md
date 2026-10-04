# 3687. Library Late Fee Calculator

**Difficulty:** Easy
**Pattern:** Simulation / Tiered Piecewise Function / Stateless Map → Sum
**Date Solved:** 2026-06-10
**Status:** ✅

---

## Understanding the Goal

For each `daysLate[i]`, compute a per-tier penalty and sum the results.

**Three tiers:**

| Condition | Penalty |
|-----------|---------|
| `d == 1` | `1` |
| `2 ≤ d ≤ 5` | `2 * d` |
| `d > 5` | `3 * d` |

**Key insight:** Each book's penalty is **independent** — depends only on its own `daysLate[i]`. This is the **"stateless map → sum"** idiom (8th encounter on the log: LC 2011, 3110, 3945, 3146, etc.):

```
total = sum(f(x) for x in items)
```

Where `f(x)` is the tiered piecewise function. The structure repeats; only `f` changes per problem.

---

## LAYER 1: Line-by-Line Explanation

### Pythonic — generator + if/elif inside

```python
class Solution:
    def lateFee(self, daysLate: List[int]) -> int:
        # Per-book penalty function — purely depends on d
        def penalty(d):
            if d == 1:
                return 1
            elif d <= 5:        # d already ≥ 2 here (the == 1 branch was taken if d == 1)
                return 2 * d
            else:               # d > 5
                return 3 * d
        
        # "Stateless map → sum" — same skeleton as LC 2011, 3110, 3945, 3146
        return sum(penalty(d) for d in daysLate)
```

**Why `elif d <= 5` and not `elif 2 <= d <= 5`:**

The first branch ate `d == 1`. By the time we reach `elif`, `d` is already `>= 2`. Just check the upper bound. Cleaner.

**Why use a generator over a list comp:**

`sum(... for ... in ...)` — passes a generator (lazy, O(1) extra space).
`sum([... for ... in ...])` — builds a list first (eager, O(n) extra space).

Both produce the same answer. Generator is cleaner for `sum()`.

### Inline conditional (one-liner)

```python
class Solution:
    def lateFee(self, daysLate: List[int]) -> int:
        # Ternary chain: read as "1 if d==1, else 2d if d<=5, else 3d"
        return sum(1 if d == 1 else 2 * d if d <= 5 else 3 * d for d in daysLate)
```

**Decoding the ternary chain:**

```
1 if d == 1 else (2 * d if d <= 5 else 3 * d)
```

Compact but harder to read for newcomers.

### Explicit for-loop (most readable)

```python
class Solution:
    def lateFee(self, daysLate: List[int]) -> int:
        total = 0
        for d in daysLate:
            if d == 1:
                total += 1
            elif d <= 5:
                total += 2 * d
            else:
                total += 3 * d
        return total
```

For interviews where clarity > cleverness, this is the safest. Whiteboard-friendly.

### Multiplier lookup (premature optimization but conceptually instructive)

```python
class Solution:
    def lateFee(self, daysLate: List[int]) -> int:
        # Multiplier per d — bounded range → table lookup possible
        multipliers = {1: 1, **{i: 2 for i in range(2, 6)}, **{i: 3 for i in range(6, 101)}}
        return sum(multipliers[d] * d for d in daysLate)
```

For tiny n it's overkill, but shows how piecewise functions can become **table lookups** when input range is bounded.

### Functional with helper

```python
def _penalty(d):
    if d == 1: return 1
    if d <= 5: return 2 * d
    return 3 * d

class Solution:
    def lateFee(self, daysLate):
        return sum(map(_penalty, daysLate))
```

`map(func, iterable)` is the functional cousin of a generator expression.

---

## LAYER 2: Worked Examples

### Example 1: `daysLate = [5, 1, 7]` → 32

| d | tier | penalty |
|---|------|---------|
| 5 | `2 ≤ d ≤ 5` | 2 × 5 = 10 |
| 1 | `d == 1` | 1 |
| 7 | `d > 5` | 3 × 7 = 21 |

Sum: 10 + 1 + 21 = **32** ✓

### Example 2: `daysLate = [1, 1]` → 2

Both hit `d == 1` → penalty 1 each. Sum: **2** ✓

### Boundary trace — tier edges

| d | tier check | penalty |
|---|------------|---------|
| 1 | `d == 1` ✓ | 1 |
| 2 | `d <= 5` ✓ | 4 |
| 5 | `d <= 5` ✓ | 10 |
| 6 | falls through to else | 18 |
| 100 | falls through to else | 300 |

Edge case: d=2 — sometimes people misread "between 2 and 5" as "strictly between." Watch the inclusive bounds.

### Edge cases

- **All d=1:** Every penalty is 1; total = n
- **All d ≥ 6:** Every penalty is 3d; total = 3 × sum(daysLate)
- **Single book:** Loop runs once; result = penalty(daysLate[0])
- **Max n=100, max d=100:** Worst case ~30,000. Trivially fast.

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **Per-element function + sum** | **O(n)** | **O(1)** | **Standard** |
| Inline ternary | O(n) | O(1) | Compact, slightly cryptic |
| Lookup table | O(n + R) | O(R) | Overkill here |

**The "stateless map → sum" idiom (8th encounter, MEMORIZE):**

```
total = sum(f(x) for x in items)
```

When each element's contribution depends ONLY on itself (no state, no neighbors, no cumulative), this is the canonical Python form. Seen in:

| Problem | f(x) |
|---------|------|
| LC 2011 (Day 8) Final Value | `+1` or `-1` based on string content |
| LC 3110 (Day 9) Score of String | `abs(ord(a) - ord(b))` per pair |
| LC 3945 (Day 10) Digit Score | digit × weight |
| LC 3146 (Day 11) Permutation Diff | `abs(idx_s[ch] - i)` |
| **LC 3687 (this)** | **Tiered penalty** |

**Recognizing this pattern is faster than coding.** When you see "compute X per element, return total," the answer is `sum(f(x) for x in items)`. Only `f` changes.

**Boundary handling — the off-by-one trap:**

```python
elif 2 <= d <= 5:    # technically correct but redundant
```

Since the `if d == 1` already exited for d=1, anything reaching `elif` has d ≥ 2. The `2 <= d` is implied. **Don't re-check conditions that earlier branches already guaranteed false.**

**The "tiered function" pattern:**

Tiered/piecewise functions are common in interviews:
- Tax brackets, shipping costs, difficulty bonuses, scoring rules, late fees, fines

**Template:**

```python
def tier(x):
    if x < BOUND_1: return f1(x)
    if x < BOUND_2: return f2(x)
    return f3(x)
```

Watch for:
- **Inclusive vs exclusive bounds**
- **Boundary points** (test d=1, d=2, d=5, d=6 explicitly)
- **Edge cases at min/max**

**Why this is "Simulation":**

Pure mechanical translation of a rule into code. No clever data structure, no algorithm — just walk and apply. The skill is **clean code organization**, not algorithm design.

---

## LAYER 4: Interview Variations

• **Max penalty (not sum):** `max(penalty(d) for d in daysLate)`.
• **Min penalty:** `min(penalty(d) for d in daysLate)`.
• **List of penalties:** `[penalty(d) for d in daysLate]`.
• **Average:** `sum(...) / len(daysLate)`.
• **Add 4th tier (>10: 4d):** Add an `elif` before else.
• **Cap penalty at MAX_FEE:** `min(penalty(d), MAX_FEE)` inside sum.
• **Discount for same patron:** Group by patron, apply rule.
• **Refund overpayment:** Subtract from balance; return max(0, total).
• **Date-based penalty:** Compute days from due_date.
• **Tiered from input:** `tiers = [(threshold, multiplier), ...]`, iterate.
• **First N books free:** Skip first n before summing.
• **Per book category:** Separate function per type.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — helper function + sum
class Solution:
    def lateFee(self, daysLate: List[int]) -> int:
        def penalty(d):
            if d == 1:
                return 1
            elif d <= 5:
                return 2 * d
            else:
                return 3 * d
        return sum(penalty(d) for d in daysLate)
```

**Inline ternary one-liner:**
```python
def lateFee(self, daysLate):
    return sum(1 if d == 1 else 2 * d if d <= 5 else 3 * d for d in daysLate)
```

**Explicit for-loop (interview-safe):**
```python
def lateFee(self, daysLate):
    total = 0
    for d in daysLate:
        if d == 1:
            total += 1
        elif d <= 5:
            total += 2 * d
        else:
            total += 3 * d
    return total
```

**Lookup table:**
```python
def lateFee(self, daysLate):
    multipliers = {1: 1, **{i: 2 for i in range(2, 6)}, **{i: 3 for i in range(6, 101)}}
    return sum(multipliers[d] * d for d in daysLate)
```

**Map with module-level helper:**
```python
def _penalty(d):
    if d == 1: return 1
    if d <= 5: return 2 * d
    return 3 * d

class Solution:
    def lateFee(self, daysLate):
        return sum(map(_penalty, daysLate))
```

---

**Time:** O(n) | **Space:** O(1)

**Pattern flag:** "For each element, compute a tier-based value; return total" → **`sum(piecewise(x) for x in items)`** — the stateless map → sum idiom (8th encounter). Define the piecewise function with `if/elif/else`, drop redundant bound checks once earlier branches have ruled them out.

The bigger lesson: **recognize when you've seen a pattern enough that it's reflex.** Today's problem is the same skeleton as LC 2011, 3110, 3945, 3146. The piecewise function changes; the wrapping doesn't. This is the **"transfer" stage of pattern internalization**.

For tiered/piecewise functions in general: inclusive/exclusive bounds matter; simplify checks based on what earlier branches excluded; explicitly test boundary values before submitting.
