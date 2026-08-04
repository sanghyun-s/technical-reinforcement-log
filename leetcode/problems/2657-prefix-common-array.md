# 2657. Find the Prefix Common Array of Two Arrays

| | |
|---|---|
| **Difficulty** | Medium |
| **Family** | Hash / Frequency (permutation trick) |
| **Date** | 2026-07-31 |
| **Mode** | Reference Recall — **Solid** (clean, no multi-attempt struggle) |
| **Link** | https://leetcode.com/problems/find-the-prefix-common-array-of-two-arrays/ |

---

## 1. Input / Output
```
input  → A = [1,3,2,4], B = [3,1,2,4]
output → [0,2,3,4]
```
`C[i]` = count of values present in *both* A[:i+1] and B[:i+1].

## 2. Data structure used
- A frequency array (best), or two sets, or two integer bitmasks.

## 3. Why this approach works
**The permutation guarantee is the key.** Because A and B are each permutations of 1..n, any
value appears once in A and once in B overall. So in a *combined* frequency count over the two
prefixes, a value hitting **count 2** must have appeared in both prefixes → it's common. Just
count when `freq[v] == 2`; no set membership needed. That "exploit the structural promise" move
is the same as the BST-ordering insights — here the promise is permutation-ness.

## 4. Time / space complexity
- Frequency / set / bitmask: **O(n)**. Brute force (triple loop): O(n³) — baseline only.

---

## My solutions (4, O(n³) → O(n))
```python
# 2 — frequency counter, the smartest: uses the permutation property (SUBMIT)
freq = [0]*(n+1); common = 0; result = []
for i in range(n):
    freq[A[i]] += 1
    if freq[A[i]] == 2: common += 1
    freq[B[i]] += 1
    if freq[B[i]] == 2: common += 1
    result.append(common)

# 3 — two sets, O(n): note the `a != b` guard so a==b isn't double-counted
# 4 — bitmask: mask |= 1<<v; bin(mask_A & mask_B).count('1') — set ops as single CPU ops
# 1 — brute force O(n³), baseline only
```

## What tripped me up
Nothing — this one clicked cleanly, a good contrast to the digit-DP grind the same night. The
`a != b` guard in the set version (Approach 3) is the one subtle line: without it, when `a == b`
the same common value gets counted twice.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | Owed (should be quick — this one landed) |
| **Where I got stuck** | — |

## Interview sentence
> Because A and B are permutations, a value whose combined frequency hits 2 must be in both
> prefixes — so I just count when frequency reaches 2, O(n), no sets needed. The set and bitmask
> versions also work, but the permutation property makes the frequency counter the cleanest.
