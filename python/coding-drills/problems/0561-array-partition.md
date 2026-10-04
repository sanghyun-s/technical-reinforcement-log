# 561. Array Partition

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | Greedy / Sorting (+ counting sort) |
| **Date** | 2026-08-16 |
| **Mode** | Reference Recall (A1 hand-typed/submitted, A2–4 from archive) |
| **Link** | https://leetcode.com/problems/array-partition/ |

---

## 1. Input / Output
```
input  → nums = [6,2,6,5,1,2]
output → 9   (pairs (1,2),(2,5),(6,6) → 1+2+6)
```
Pair the 2n numbers to maximize Σ min(each pair).

## 2. The greedy + why it's optimal
Sort ascending, sum every **even-indexed** element (`nums[0], nums[2], …`). In each pair you
lose the larger element (only `min` counts), so to lose as little as possible, **pair each
number with its nearest neighbor** — minimizing the sacrificed max. Proof sketch: if any two
paired numbers aren't adjacent, swapping to make them adjacent never does worse ⇒ adjacent
pairing is optimal. (Rearrangement-flavored greedy, same family as 1874.)

## 3. Two ideas: sort vs counting sort
- **Sort (A2–4), O(n log n):** `sum(sorted(nums)[::2])` — `[::2]` = even indices = the mins.
- **Counting sort (A1), O(n + K):** tally frequencies, walk values low→high taking every even
  count. Bounded values (−10⁴..10⁴) make it linear. **`+ K` offset trick:** shift by K=10000 so
  negatives index a bucket array (−10⁴ → index 0). Reusable "offset for negatives" pattern.

## 4. The judgment (recurring)
At n ≤ 10⁴, counting sort is **over-engineering** — the sort is ~140K ops; counting sort walks a
20,001-element array regardless of input. Plain sort is simpler and about as fast. Same
"bounded values makes counting sort *available* but the simple sort usually wins at these sizes"
call as 2974/2545 — with 1365 as the counter-case where counting sort was genuinely optimal.

---

## My solutions
```python
# 2 — clean one-liner (submit): sum the min of each adjacent pair
return sum(sorted(nums)[::2])

# 4 — .sort() then sum(nums[::2])  (mutates nums vs A2's sorted() — mutate-vs-return)
# 3 — explicit loop, range(0, len, 2)
# 1 — counting sort with +K offset for negatives, O(n+K)  (over-engineering at n<=1e4)
```

## Interview sentence
> Sort ascending and sum every other element from index 0. Optimal because each pair loses its
> larger element, so pairing adjacent values minimizes the loss — swapping any non-adjacent pair
> to adjacent never does worse. O(n log n). Values are bounded so counting sort gives O(n), but at
> n ≤ 10⁴ the plain sort is simpler and about as fast.
