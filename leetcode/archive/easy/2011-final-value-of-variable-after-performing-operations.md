# 2011. Final Value of Variable After Performing Operations

**Difficulty:** Easy
**Pattern:** Simulation / String Inspection
**Date Solved:** 2026-06-04
**Status:** ✅

---

## Understanding the Goal

Walk a list of "++X", "X++", "--X", "X--" operations. Each is +1 or -1. Return final X (starting at 0).

**Key insight:** Only the **operator** matters, not X's position. Each op is +1 or -1.

To decide +1 or -1, inspect any unique character. Editorial picks `op[1]` (middle char):
- `"++X"` → middle `'+'` → +1
- `"X++"` → middle `'+'` → +1
- `"--X"` → middle `'-'` → -1
- `"X--"` → middle `'-'` → -1

Brilliant: the middle character is ALWAYS `'+'` or `'-'`, regardless of X's position. One check disambiguates all 4 cases.

---

## LAYER 1: Line-by-Line Explanation

**Editorial one-liner:**

```python
class Solution:
    def finalValueAfterOperations(self, operations: List[str]) -> int:
        return sum(1 if op[1] == "+" else -1 for op in operations)
```

- `for op in operations` — iterate every 3-character string
- `op[1]` — middle character
- `1 if op[1] == "+" else -1` — convert to ±1
- `sum(...)` — total

**Why `op[1]` works for all four:**

```
op[0]  op[1]  op[2]
 +      +      X      "++X"
 X      +      +      "X++"
 -      -      X      "--X"
 X      -      -      "X--"
```

Middle is always the operator (position 0 or 2 is X, never position 1).

**Why this is "just a return":**

Each output element depends only on one input element (stateless per element). No accumulator variable, no conditional split across lines. List comprehension or generator handles it directly.

**Explicit loop (most readable):**

```python
class Solution:
    def finalValueAfterOperations(self, operations: List[str]) -> int:
        x = 0
        for op in operations:
            if op[1] == "+":
                x += 1
            else:
                x -= 1
        return x
```

**Using "in" substring check:**

```python
def finalValueAfterOperations(operations):
    return sum(1 if "+" in op else -1 for op in operations)
```

Works because each operation has either `+` or `-`, never both.

---

## LAYER 2: Worked Examples

**Example 1: `["--X","X++","X++"]` → 1**

| op | op[1] | contribution | running X |
|----|-------|--------------|-----------|
| "--X" | '-' | -1 | -1 |
| "X++" | '+' | +1 | 0 |
| "X++" | '+' | +1 | 1 |

Sum: -1 + 1 + 1 = **1** ✓

**Example 2: `["++X","++X","X++"]` → 3** (all +1's) ✓

**Example 3: `["X++","++X","--X","X--"]` → 0**

| op | op[1] | contribution |
|----|-------|--------------|
| "X++" | '+' | +1 |
| "++X" | '+' | +1 |
| "--X" | '-' | -1 |
| "X--" | '-' | -1 |

Sum: 0 ✓ (balanced)

**Walkthrough of generator on Example 1:**

```
Iter 1: op="--X", op[1]='-', yields -1
Iter 2: op="X++", op[1]='+', yields +1
Iter 3: op="X++", op[1]='+', yields +1
sum([-1, +1, +1]) = 1
```

**Edge cases:**
- Single op: `["X++"]` → 1
- All increments: `["++X"]*100` → 100
- All decrements: `["--X"]*100` → -100
- Empty list: constraints say ≥1, but `sum([])` would safely return 0

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| `sum(... op[1] == "+" ...)` | O(n) | O(1) | **Cleanest one-liner** |
| Loop with if/else | O(n) | O(1) | Most readable |
| `"+" in op` check | O(n × 3) | O(1) | Same big-O, slightly slower |
| Counting + arithmetic | O(n) | O(1) | (see below) |

**Counting alternative:**

```python
def finalValueAfterOperations(operations):
    plus = sum(1 for op in operations if op[1] == '+')
    return 2 * plus - len(operations)
```

Total = `p - m` where `p + m = n` → `2p - n`.

**Two recognition skills this tests:**

1. **What's the invariant?** Only +/- matters; X position is decoration.
2. **What's a distinguishing character?** `op[1]` cleanest — works for all 4 cases. `op[0]` or `op[2]` would need 2-condition check (one always X).

**The "stateless map → sum" pattern:**

> If each input produces value INDEPENDENTLY of other inputs and you need a total → `sum(f(x) for x in items)`.

Covers:
- Count matches: `sum(1 for x in items if cond(x))`
- Sum of squares: `sum(x*x for x in nums)`
- Hamming-like: `sum(a != b for a, b in zip(s1, s2))`
- Conditional ±1 (this): `sum(+1 if cond else -1 for x in items)`

**Why both 1920 and 2011 are "just return" problems:**

Both:
1. Output fully determined by independent per-element transforms
2. No state flows between elements (other than what `sum`/listcomp handles)
3. Single value or collection result

When these hold, skip the boilerplate. Not a trick — Python lets declarative replace imperative when logic supports it.

---

## LAYER 4: Interview Variations

• **More operators (×, /):** Need running value (not commutative). Use `reduce(apply_op, ops, 0)`.
• **Start at K instead of 0:** `K + sum(...)`.
• **Multiple variables (X, Y, Z):** Track a dict; update the right one per op.
• **Variable step sizes (X+=5):** Parse the int from the string.
• **Running history:** `list(accumulate(±1 generator))`.
• **Max/min X ever reached:** Track running with max/min update.
• **First op making X negative:** Track running; return index.
• **Longer variable name (counter++):** Detect with `op[-1] == "+"`.
• **Modulo N:** Take `result % N`.
• **Boolean toggle:** XOR with running bit.
• **Streaming from file/socket:** Same logic, one at a time.
• **Parallel batches:** Each ±1 independent → embarrassingly parallel.
• **Weighted ops:** Compute weighted sum.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — editorial one-liner
class Solution:
    def finalValueAfterOperations(self, operations: List[str]) -> int:
        return sum(1 if op[1] == "+" else -1 for op in operations)
```

**Explicit loop:**
```python
def finalValueAfterOperations(operations):
    x = 0
    for op in operations:
        if op[1] == "+":
            x += 1
        else:
            x -= 1
    return x
```

**Using "in":**
```python
def finalValueAfterOperations(operations):
    return sum(1 if "+" in op else -1 for op in operations)
```

**Counting + arithmetic:**
```python
def finalValueAfterOperations(operations):
    plus = sum(1 for op in operations if op[1] == "+")
    return 2 * plus - len(operations)
```

**Pattern matching (Python 3.10+):**
```python
def finalValueAfterOperations(operations):
    x = 0
    for op in operations:
        match op:
            case "++X" | "X++": x += 1
            case "--X" | "X--": x -= 1
    return x
```

**Dict lookup:**
```python
def finalValueAfterOperations(operations):
    deltas = {"++X": 1, "X++": 1, "--X": -1, "X--": -1}
    return sum(deltas[op] for op in operations)
```

---

**Time:** O(n) | **Space:** O(1)

**Pattern flag:** "Apply N independent operations and return final aggregate" → `sum(transform(x) for x in items)`. The Pythonic skill: compressing imperative simulation into a declarative one-liner when per-element logic is stateless. When you spot the "just a return" pattern, it's a hint you've identified the most concise solution.
