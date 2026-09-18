# 3450. Maximum Students on a Single Bench

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | Hash / Grouping |
| **Date** | 2026-09-18 |
| **Mode** | Reference Recall (A1 rewritten/submitted, A2–A4 hand-typed from archive) |
| **Link** | https://leetcode.com/problems/maximum-students-on-a-single-bench/ |

---

## 1. Input / Output
```
input  → students = [[1,2],[2,2],[3,3],[1,3],[2,3]]
output → 3   (bench 3 has unique students {1,2,3})
```
Max number of **unique** students on any one bench. Empty input → 0.

## 2. The idea — group by bench, dedupe within
Two parts: (1) group students by bench, (2) **dedupe within each bench** — a student can repeat
on a bench but counts once. That second part is *why the value type is a `set`*: `set.add`
silently drops duplicates, so "unique per bench" falls out for free. Then take the max set size,
`default=0` for empty input (Example 4). Pattern: **count *unique* things per group → dict of sets.**

## 3. Complexity
O(n) — one pass to group, one pass over ≤100 benches for the max; set ops O(1) average.

---

## My solutions
```python
# 1/2 — defaultdict(set): auto-creates an empty set on first access (cleanest)
bench = defaultdict(set)
for sid, bid in students:
    bench[bid].add(sid)                      # set dedupes automatically
return max((len(s) for s in bench.values()), default=0)

# 3 — dict.setdefault(bid, set()).add(sid): manual version — but builds a throwaway set()
#     on EVERY call (arg evaluated before the key check); defaultdict only creates when needed
# 4 — dedupe-first: set(tuple(p) for p in students), then count pairs per bench
#     (tuple() required — lists aren't hashable). A different strategy, not just an idiom.
```

## What tripped me up
Nothing broke. The key judgment: **`Counter` vs `defaultdict(set)`** — 3541 used `Counter`
(counting occurrences); this uses `defaultdict(set)` (collecting distinct members). Same "group
by key" shape, value type chosen by whether duplicates matter. `defaultdict` over `setdefault`
because the latter constructs a throwaway set on every call.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | Owed |
| **Where I got stuck** | — |

## Interview sentence
> Group by bench with a `defaultdict(set)` — the set dedupes students automatically, which is the
> point since a student can repeat but counts once — then take the max set size, `default=0` for
> empty input. `Counter` when I want counts, `defaultdict(set)` when I want distinct membership.
