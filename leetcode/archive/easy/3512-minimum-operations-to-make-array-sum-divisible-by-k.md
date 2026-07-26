# 3512. Minimum Operations to Make Array Sum Divisible by K

**Difficulty:** Easy
**Pattern:** Math / Modular Arithmetic / "Mathematical Collapse"
**Date Solved:** 2026-06-10
**Status:** ✅

---

## Understanding the Goal

You can decrease any element by 1 per operation. Find the **minimum** operations to make the sum divisible by k.

**The math collapse:**

Each operation decreases the **sum by exactly 1**. To make `sum` divisible by `k`, you need to drop down to the **next lower multiple of k**. The "distance" to the next lower multiple is:

```
distance = sum % k
```

That's it. The answer is literally `sum(nums) % k`. The hint gives it away.

This is a **"mathematical collapse"** problem — looks like simulation/greedy, collapses to a single math expression. Recognizing the collapse is the entire skill.

---

## LAYER 1: Line-by-Line Explanation

### The whole solution

```python
class Solution:
    def minOperations(self, nums: List[int], k: int) -> int:
        # sum(nums) gives current total
        # % k gives remainder when divided by k
        # That remainder = number of -1 operations needed to reach next lower multiple of k
        # Why? Each op decreases sum by exactly 1; we need to drop by exactly `remainder`
        return sum(nums) % k
```

**Why each piece matters:**

- `sum(nums)`: The current total. Each operation decreases this by exactly 1.
- `% k`: The remainder of dividing by k. If `sum = 17` and `k = 5`, then `17 % 5 = 2`. We're 2 above the nearest lower multiple (15).
- **Therefore:** drop by `remainder`, sum becomes a multiple of k. That's the minimum.

### Equivalent — using Python's divmod

```python
class Solution:
    def minOperations(self, nums: List[int], k: int) -> int:
        # divmod(a, b) returns (quotient, remainder)
        _, remainder = divmod(sum(nums), k)
        return remainder
```

Pedagogical — same answer.

### "Simulated" version (DON'T DO THIS — misses the insight)

```python
class Solution:
    def minOperations(self, nums: List[int], k: int) -> int:
        # The simulation tempting trap — DO NOT DO THIS
        nums = list(nums)
        ops = 0
        while sum(nums) % k != 0:
            nums[0] -= 1
            ops += 1
        return ops
```

**Why this is wrong to write (even though it produces the right answer):**

- O(sum(nums)) work in worst case
- Misses the entire point of the problem
- Demonstrates ignorance of modular arithmetic
- Interview signal: failure to recognize math collapse

The hint `sum(nums) % k` is literally TELLING you the answer is one expression. **Trust the hint.**

---

## LAYER 2: Worked Examples

### Example 1: `nums = [3, 9, 7], k = 5` → 4

```
sum = 3 + 9 + 7 = 19
19 % 5 = 4              ← need 4 operations
```

Verify: 19 - 4 = 15, which is 3 × 5. ✓

The example demonstrates operations on `nums[1] = 9 → 5` (4 operations), giving sum 15.

Note: we could ALSO drop nums[0] by 3 and nums[1] by 1 — still 4 operations. **The distribution of operations across indices doesn't matter; only the total count does.**

### Example 2: `nums = [4, 1, 3], k = 4` → 0

```
sum = 4 + 1 + 3 = 8
8 % 4 = 0               ← already divisible, no ops needed
```

✓

### Example 3: `nums = [3, 2], k = 6` → 5

```
sum = 3 + 2 = 5
5 % 6 = 5               ← need 5 operations
```

Verify: 5 - 5 = 0, which is divisible. ✓

Note: this drops both values to 0. **The problem doesn't restrict values to stay non-negative.**

### Bigger trace — `nums = [100, 200, 300], k = 7`

```
sum = 600
600 / 7 = 85.71...
85 × 7 = 595            ← next lower multiple of 7
600 - 595 = 5           ← need 5 operations
600 % 7 = 5             ← directly from the modulo
```

Result: **5** ✓

### Edge cases

- **Sum already divisible:** `sum % k == 0` → 0 operations
- **k = 1:** Everything divisible by 1; always returns 0
- **Single element:** `nums = [7], k = 5` → 7 % 5 = 2 operations
- **k > sum:** `nums = [3, 2], k = 100` → `5 % 100 = 5` operations
- **Max constraints (n=1000, vals=1000, k=100):** Sum up to 10⁶; modulo is O(1)

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **`sum(nums) % k`** | **O(n)** | **O(1)** | **The answer** |
| Simulation | O(sum × n) | O(n) | Wasteful, misses insight |

**Why the math works — modular arithmetic in plain English:**

Every positive integer `x` can be written as:
```
x = q * k + r       where 0 ≤ r < k
```

`r` is the **remainder** — how far above the nearest lower multiple of k you are. To make `x` divisible by k:
- **Decrease by exactly `r`** → reach `q * k` (divisible) → minimum cost
- Decrease by `r + k` → reach `(q-1) * k` (also divisible) → costs k more
- Decrease by `r + 2k` → costs 2k more

So **`r = sum % k`** is the minimum. Always.

**Why "any index" is fine:**

The operation only changes the sum by -1, regardless of which index you pick. The sum doesn't care WHERE the decrement happened — just THAT it happened. So this is **not actually an array problem** at the conceptual level; it's a **scalar problem**.

The "array" framing is a misdirection. The relevant quantity is `sum(nums)`, a single number. Once you see that, the array dissolves.

**The "mathematical collapse" pattern (memorize this lens):**

Some problems present themselves as simulation, optimization, or search — but actually have a closed-form math answer that collapses the whole thing.

Signs to look for:
1. **Operation changes one quantity by a fixed amount** (here, sum by -1)
2. **Goal is divisibility/parity/specific value** (modular constraint)
3. **Hint mentions modulo, sum, or a simple expression**
4. **Small constraint ranges suggesting no DP/BFS needed**

When you see these together, the answer is usually math, not algorithm.

**Problems on your log with similar "collapse" framing:**

| Problem | Apparent complexity | Actual answer |
|---------|---------------------|---------------|
| **LC 2160 Min Sum Four Digits (Day 5)** | DP/optimization? | Sort, then greedy split — closed form |
| **LC 1689 Deci-Binary (Day 5)** | DP partition? | `max(digit) in s` — closed form |
| **LC 1051 Height Checker (Day 8)** | Simulation? | Count mismatches after sorting |
| **LC 2050 Unique Digits (Day 2)** | DP? | Brute force closed-form |
| **LC 2843 Symmetric Integers (Day 8)** | DP/digit DP? | Brute force per number |
| **LC 3512 (this)** | Simulation? | **`sum(nums) % k`** |

Cultivating the **"look for the collapse"** instinct is a high-leverage interview skill. Before writing code, ask: **is there a math expression that just gives the answer?**

**The modulo as "distance to next multiple":**

A subtle reframing — instead of "remainder," think of `x % k` as "**how far is x from the next lower multiple of k?**". This framing maps directly to:

- LC 3512 (this): operations needed to drop to nearest lower multiple
- Coin change variants: how much change after collecting full denominations
- Time/clock problems: minutes past the hour

**Connection to "stateless map → sum" pattern:**

Today's LC 3687 was `sum(f(x) for x in nums)`. This is even simpler — just `sum(nums)`. The reduction is the WHOLE answer (modulo k).

Both problems share the move: **reduce array to scalar, then apply a per-scalar transform.**

```python
LC 3687:  sum(piecewise_func(x) for x in nums)
LC 3512:  sum(nums) % k
```

Different transforms; same compositional shape.

**Why no DP, no greedy, no search needed:**

DP/greedy/search techniques are needed when **choices interact**. Here, the only "choice" is which index to decrement — but the choice doesn't matter because the sum changes identically regardless. With no meaningful choices, there's no search space; with no search space, there's just arithmetic.

---

## LAYER 4: Interview Variations

• **Add operations (only +1):** Answer is `(-sum) % k` = `(k - sum % k) % k`.
• **Both +1 and -1 allowed:** `min(sum % k, k - sum % k)` — closer multiple.
• **Element-bounded (nums[i] ≥ 0):** Check budget — always works since 0 ≤ r < k ≤ sum or sum < k.
• **Sum equal to specific target T:** `abs(sum - T)`.
• **Multiple decreases per op (e.g., -k):** Doesn't change `sum mod k`. Edge case.
• **Sum mod k == specific r:** `(sum % k - r) % k`.
• **Find ALL valid configurations:** Many — but the COUNT is one number.
• **Minimize MAX element:** Different problem; can't use modulo trick.
• **Every element divisible by k:** Each `nums[i]` needs `nums[i] % k` operations.
• **Streaming nums:** Maintain running `sum % k`; O(1) per insertion.

---

## LAYER 5: Cheat Sheet

```python
# THE solution
class Solution:
    def minOperations(self, nums: List[int], k: int) -> int:
        return sum(nums) % k
```

**With divmod for pedagogical clarity:**
```python
def minOperations(self, nums, k):
    _, remainder = divmod(sum(nums), k)
    return remainder
```

**If both +1 and -1 operations allowed:**
```python
def minOperations(self, nums, k):
    r = sum(nums) % k
    return min(r, k - r)
```

**If only +1 allowed (raise sum to next multiple):**
```python
def minOperations(self, nums, k):
    return (-sum(nums)) % k    # Python handles negative modulo cleanly
```

**For making EACH element divisible:**
```python
def minOperations(self, nums, k):
    return sum(x % k for x in nums)
```

**For arbitrary target T:**
```python
def minOperations(self, nums, T):
    return abs(sum(nums) - T)
```

---

**Time:** O(n) for sum, O(1) modulo | **Space:** O(1)

**Pattern flag:** "Minimum operations where each op changes a quantity by a fixed amount, target is divisibility" → **`current_quantity % k`** (or `(target - current) % k`). The operation count is the remainder, no simulation needed.

This is the **"mathematical collapse"** family — problems framed as simulation but solvable with a closed-form arithmetic expression. Look for:
1. Single quantity changing by a fixed step per operation
2. Modular / divisibility / specific-value target
3. No meaningful choice between operations (all choices equivalent)
4. Hint mentioning modulo or sum

The **"distance to next multiple"** framing of modulo (rather than just "remainder") is a thinking tool worth keeping — it directly maps to operation counts in this kind of problem.

**Hint trust:** When a hint is a complete expression (`sum(nums) % k`), it IS the answer. No further code design needed. **Trust the hint; write the expression.**
