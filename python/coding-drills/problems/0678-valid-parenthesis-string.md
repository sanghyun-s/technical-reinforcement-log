> **Skill:** python / coding-drills (greedy / interval tracking; memoized DFS) · **Source:** LeetCode #678 · **Mode:** New · **Date:** 2026-11-01
> Difficulty: Medium · Family: Greedy / Strings

# 678. Valid Parenthesis String

## Problem
String of `(`, `)`, `*`. Each `*` can be `(`, `)`, or empty. Return whether the string *can* be made valid.

## The insight — three solutions, and the last one is the gem
The `*` wildcard makes the open-count **a range, not a number.** Instead of trying every `*` substitution (exponential), track the **interval of possible open-counts** `[cmin, cmax]` in one pass:
- `(` → both `cmin`, `cmax` += 1
- `)` → both -= 1
- `*` → `cmin -= 1` (treat as `)`) and `cmax += 1` (treat as `(`) — the range *widens*
Two guards: if **`cmax < 0`** too many `)` even using every `*` as `(` → impossible, return False. Clamp **`cmin` at 0** (never let the low end go negative — we can always treat a `*` as empty/`(`). Valid iff **`cmin == 0`** at the end (zero open brackets is reachable).

## My solutions
```python
# A3 — GREEDY interval, O(n) time / O(1) space (the one to know)
def checkValidString(self, s):
    cmin = cmax = 0
    for ch in s:
        if ch == '(':   cmin += 1; cmax += 1
        elif ch == ')': cmin -= 1; cmax -= 1
        else:           cmin -= 1; cmax += 1   # '*' widens the range
        if cmax < 0: return False              # irrecoverable excess ')'
        cmin = max(cmin, 0)                     # low end can't go below 0
    return cmin == 0

# A1/A2 — top-down memoized DFS over (index, open_count): try '*' as '(', ')', empty.
#   A2 fixed A1's bug: memo second dim must be (n+1), not n — open_count can reach n → IndexError.
```

## Mistakes & debugging (real, from my own A1→A2)
A1's `memo = [[-1]*n ...]` **IndexErrors**: `open_count` can equal `n`, so the width must be **`n+1`**. A2 fixed it. Classic off-by-one in a memo table — the same "size the array for the max index, not the count" lesson.

## Complexity
A3: O(n)/O(1). A1/A2: O(n²) states, O(n²) memo.

## Reproduce cold
The greedy `[cmin, cmax]` sweep with both guards; explain why `cmin` clamps at 0 but `cmax<0` is fatal.

## Interview line candidate
> The `*` makes the open-count a range, not a number, so I track the min and max possible open brackets in one pass: `)` lowers both, `(` raises both, `*` widens the range. If the max ever goes negative I bail, I clamp the min at zero, and it's valid if the min lands at zero. O(n), O(1) — no need to enumerate the wildcards.
