# 1265. Print Immutable Linked List in Reverse

| | |
|---|---|
| **Difficulty** | Medium |
| **Family** | Linked List (recursion / √n decomposition) |
| **Date** | 2026-08-11 |
| **Mode** | Reference Recall — solid on A1–3; A4 (√n) is **conceptual** |
| **Link** | https://leetcode.com/problems/print-immutable-linked-list-in-reverse/ |

---

## 1. Input / Output
```
input  → head = [1,2,3,4]
output → 4 3 2 1   (printed, not returned)
```
Print values in reverse using only `printValue()` / `getNext()` — **no node modification**.

## 2. Data structure used
- Call stack, explicit stack, re-walking, or √n checkpoints — depending on the space budget.

## 3. Why this approach works
The immutability constraint forbids in-place reversal, so reversal must come from **external
ordering**. The four approaches are **points on a space/time frontier**:
- **Recursion — O(n) time / O(n) space:** recurse to the end, print on unwind (call stack reverses).
- **Explicit stack — O(n) / O(n):** same, iterative; dodges recursion-depth limits.
- **Brute force — O(n²) / O(1):** re-walk from head to each position from the back. Buys O(1)
  space, pays in time. (Answers "constant space.")
- **√n decomposition — O(n) / O(√n):** store √n checkpoints spaced √n apart; process chunks
  back-to-front, recursing ≤ √n deep within each. Meets the "less than linear space" follow-up —
  neither store-everything nor store-nothing, but √n of each.

**√n decomposition is a general technique:** when O(n) space is too much and O(n²) time too slow,
store √n landmarks and do O(√n) local work between them (cf. sqrt decomposition / Mo's algorithm).

## 4. Time / space complexity
- Recursion/stack: O(n)/O(n) · brute: O(n²)/O(1) · **√n: O(n)/O(√n)**.

---

## My solutions
```python
# 1 — recursion: recurse to end, print on the way back
if head:
    self.printLinkedListInReverse(head.getNext())
    head.printValue()

# 2 — explicit stack: walk forward pushing, pop LIFO to print reversed
# 3 — O(1) space: find length n, re-walk i steps from head for i = n-1..0  (O(n^2))
# 4 — sqrt(n): store ~sqrt(n) checkpoints, print chunks last->first, recurse <= sqrt(n) deep
```

## What tripped me up
Nothing broke. The teeth of the problem: **immutability forbids in-place reversal**, so every
approach reverses via external structure (stack / re-walk / checkpoints). √n decomposition
(Approach 4) is heavy — its honest next rep is conceptual re-explanation, not cold retype.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | A1–A3 owed (small). A4: re-explain the √n idea, don't retype |
| **Where I got stuck** | — |

## Interview sentence
> Recursion or an explicit stack is O(n) time and space. For O(1) space I'd re-walk from head
> each time, but that's O(n²). The sweet spot is √n decomposition: store √n checkpoints and
> recurse √n deep within each chunk — O(n) time, O(√n) space — because immutability means I must
> reverse via external landmarks, not in place.
