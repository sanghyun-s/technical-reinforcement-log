# 3831. Median of a Binary Search Tree Level

| | |
|---|---|
| **Difficulty** | Medium |
| **Family** | Trees (BST / BFS) |
| **Date** | 2026-07-31 |
| **Mode** | Reference Recall — **Shaky** (recursive rebuild passed on 3rd attempt) |
| **Link** | https://leetcode.com/problems/median-of-a-binary-search-tree-level/ |

---

## 1. Input / Output
```
input  → root = [6,3,8], level = 1
output → 8    (level-1 values [3,8]; even count → upper median → 8)
```
Return the **upper** median of all values at `level`; `-1` if the level is empty.

## 2. Data structure used
- BFS queue (`deque`) to isolate the level, or DFS with a depth counter.

## 3. Why this approach works
Gather every value at the target level, then take the median. The upper-median rule means
`values[n // 2]` is correct for **both** parities: n=2 → index 1 (larger of two middles),
n=3 → index 1 (middle). (Lower median would be `(n-1)//2` — the off-by-one an interviewer probes.)

**The key optimization (Approach 3):** a BST traversed level-by-level, **left-to-right**,
emits each level **already sorted ascending** — because every left child < node < right child,
and BFS visits left-before-right across the level. So no sort is needed: index the median
directly. Same "use the BST property to skip work" move as Range Sum's pruning.

## 4. Time / space complexity
- Approaches 1 & 2: O(n) to reach the level + **O(k log k)** to sort that level.
- Approach 3: **O(n), no sort** — the BST-already-sorted insight removes the k log k term.

---

## My solutions (3, all correct)
```python
# 1 — BFS level-by-level, then sort (submitted)
queue, current_level = deque([root]), 0
while queue and current_level < level:
    for _ in range(len(queue)):
        node = queue.popleft()
        if node.left:  queue.append(node.left)
        if node.right: queue.append(node.right)
    current_level += 1
if not queue: return -1
values = sorted(n.val for n in queue)
return values[len(values) // 2]

# 2 — recursive DFS gathering values at depth == level, then sort
# (this is the one that took 3 attempts)

# 3 — BFS, NO SORT: BST + left-to-right BFS => level already ascending
queue = deque([root])
for _ in range(level):
    if not queue: return -1
    queue = deque(child for node in queue
                        for child in (node.left, node.right) if child)
if not queue: return -1
return queue[len(queue) // 2].val
```

## What tripped me up
The recursive DFS (Approach 2) needed **3 attempts** — recall was shaky, hence the grade.
Correctness subtlety in Approach 3: the "already sorted" claim only holds if children are
pushed **left-then-right**. My generator `(node.left, node.right)` preserves that; writing
`(node.right, node.left)` would silently break the median while still running. That's the
fragile line — got it right, but worth knowing *why* it's fragile.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | **Owed** — priority: the recursive DFS (the shaky one) |
| **Where I got stuck** | Recursive version took 3 tries; rebuild it clean |

## Interview sentence
> BFS gathers a level, but because it's a BST the level comes out already sorted left-to-right,
> so I index the upper median directly instead of sorting — O(n) with no k log k term. The
> "already sorted" trick only holds if I push children left-then-right.
