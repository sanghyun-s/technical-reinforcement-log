# 338. Counting Bits

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | DP / Bit manipulation |
| **Date** | 2026-08-16 |
| **Mode** | Reference Recall (A1 rewritten/submitted, A2 hand-typed from archive) |
| **Link** | https://leetcode.com/problems/counting-bits/ |

---

## 1. Input / Output
```
input  → n = 5
output → [0,1,1,2,1,2]   (1-bit counts of 0..5)
```
Return 1-bit counts for every i in 0..n. No built-in popcount.

## 2. Two tiers (the follow-up: O(n log n) → O(n))
**A1 — Brian Kernighan, O(n log n).** `x &= x - 1` **clears the lowest set bit** each loop, so
it runs once per 1-bit. (Subtracting 1 flips the lowest set bit + sets everything below; AND
wipes them.) Counting one number is O(popcount); over 0..n it's O(n log n).

**A2 — DP, O(n) single pass (the star).** `ans[i] = ans[i >> 1] + (i & 1)`. The bit count of `i`
= bit count of `i` with its last bit removed (`i >> 1` = `i // 2`, already computed) **plus**
whether that last bit was set (`i & 1`). O(1) per entry given earlier answers → O(n) total.
- **Parity view (Hint 3):** `i & 1` is "is i odd," so: *bits(i) = bits(i//2) + (1 if i odd)*.
  Even = its half shifted left (same count); odd = that plus the trailing 1.

## 3. The pattern
A2 is a **DP** — each answer built from strictly smaller answers already in the table (Fibonacci-
style, per Hint 1 "use what you've produced already"). The transferable move: "count bits for
*all* numbers up to n" is a DP problem, not a bit-twiddling one — same "reuse smaller answers /
carry forward" idea as 538 and 1365, collapsing O(n log n) → O(n).

## 4. Complexity
A1 O(n log n) · A2 **O(n)**, single pass, O(n) output.

---

## My solutions
```python
# 1 — Kernighan popcount per number, O(n log n)
def pc(x):
    c = 0
    while x: x &= x - 1; c += 1     # clears the lowest set bit
    return c
return [pc(x) for x in range(n + 1)]

# 2 — DP, O(n): ans[i] = ans[i >> 1] + (i & 1)
ans = [0] * (n + 1)
for i in range(1, n + 1):
    ans[i] = ans[i >> 1] + (i & 1)
return ans
```

## Interview sentence
> For one number, `x &= x-1` clears the lowest set bit and counts in O(popcount). But for all
> numbers 0..n the O(n) answer is DP: `ans[i] = ans[i>>1] + (i&1)` — i's bit count is its half's
> count plus its last bit, and the half is already computed. Same "reuse smaller answers" idea as
> Fibonacci.
