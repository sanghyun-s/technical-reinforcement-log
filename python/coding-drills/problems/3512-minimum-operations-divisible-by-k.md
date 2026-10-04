# 3512. Minimum Operations to Make Array Sum Divisible by K

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | Math (collapse) |
| **Date** | 2026-09-14 |
| **Mode** | Reference Recall (A1 rewritten/submitted, A2/A3 hand-typed from archive) |
| **Link** | https://leetcode.com/problems/minimum-operations-to-make-array-sum-divisible-by-k/ |

---

## 1. Input / Output
```
input  → nums = [3,9,7], k = 5
output → 4   (sum 19 → 15, four decrements)
```
Each op subtracts 1 from any element; minimise ops to make the sum divisible by k.

## 2. The insight — the operation description is a disguise
Every operation drops the **total sum** by exactly 1, and **which index you pick is irrelevant**.
So "minimum decrements to reach a multiple of k" = "how far is the sum above the nearest lower
multiple of k" = **`sum(nums) % k`**. The per-index choice is a decoy. A **mathematical collapse**
— same family as 3945 (score = digit sum) and the archive's collapse trifecta.

## 3. Complexity
O(n) for the sum, O(1) space. The naive simulation (A3) is O(n·k) — recomputes the sum per decrement.

---

## My solutions
```python
# 1 — the answer
return sum(nums) % k

# 2 — divmod (returns (quotient, remainder); discard the quotient) — same result, extra ceremony
_, remainder = divmod(sum(nums), k)
return remainder

# 3 — the simulation TRAP (do not do this): loop decrementing nums[0], recompute sum each time.
# Correct but O(n·k) — kept as a labelled reminder of what the collapse saves.
```

## What tripped me up
Nothing — flagged the simulation myself as the trap. The lesson is recognising the collapse
*before* writing machinery: the operation spec (select index, decrement, minimise) reads like an
optimisation but is a one-liner.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | Owed (trivial — value is spotting the collapse) |
| **Where I got stuck** | — |

## Interview sentence
> Every operation drops the total sum by 1 and the index doesn't matter, so "minimum decrements to
> a multiple of k" is just `sum(nums) % k` — how far the sum sits above the lower multiple. The
> operation description is a disguise over a one-liner; the naive simulation is O(n·k) for the same
> answer.
