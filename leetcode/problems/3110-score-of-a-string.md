# 3110. Score of a String

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | Strings (adjacent-pair) |
| **Date** | 2026-09-14 |
| **Mode** | Reference Recall (A1 submitted, A2 hand-typed from archive) |
| **Link** | https://leetcode.com/problems/score-of-a-string/ |

---

## 1. Input / Output
```
input  → s = "hello"
output → 13   (|h-e|+|e-l|+|l-l|+|l-o| = 3+7+0+3)
```
Sum of absolute ASCII differences between adjacent characters.

## 2. The idea — adjacent-pair scan
Slide a window of two across the string; sum `abs(ord(a) - ord(b))` per pair. `ord()` → ASCII
int; `abs()` because a difference goes either way and both cost positive (same as 1165's finger
distance). No trick — the problem is exactly what it says.

## 3. Complexity
O(n) — one pass over n−1 pairs; `ord`/`abs` are O(1).

---

## My solutions
```python
# 2 — idiomatic one-liner (submit): zip pairs each char with its successor, stops at the shorter
return sum(abs(ord(a) - ord(b)) for a, b in zip(s, s[1:]))

# 1 — index loop: range(len(s) - 1) stops one early so s[i+1] never runs off the end
score = 0
for i in range(len(s) - 1):
    score += abs(ord(s[i]) - ord(s[i+1]))
return score
```

## What tripped me up
Nothing. Two things I got right: the `range(len(s)-1)` boundary (going to `len(s)` → IndexError
on `s[i+1]`), and using `zip(s, s[1:])` which handles that off-by-one **structurally** — `zip`
stops at the shorter sequence, so the offset slice yields exactly n−1 pairs. `zip(s, s[1:])` and
`pairwise(s)` (3.10+, seen in 1637) are interchangeable — the latter is the purpose-built version.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | Owed |
| **Where I got stuck** | — |

## Interview sentence
> It's an adjacent-pair scan — `sum(abs(ord(a) - ord(b)) for a, b in zip(s, s[1:]))`. `zip` with
> the offset slice pairs each char with its neighbour and stops at the shorter one, so the
> off-by-one handles itself. O(n).
