# 3846. Total Distance to Type a String Using One Finger

**Difficulty:** Medium
**Pattern:** String / Hash Map / Manhattan Distance / Adjacent Pair Aggregation
**Date Solved:** 2026-06-05
**Status:** ✅

---

## Understanding the Goal

Keyboard grid:

```
Row 0: q w e r t y u i o p
Row 1: a s d f g h j k l
Row 2: z x c v b n m
```

For each char in `s`, finger travels Manhattan distance `|r1 − r2| + |c1 − c2|`. Sum all distances. Finger starts at `'a'` = (1, 0).

**Key insights:**
1. **Position lookup is fixed and small** — build a `char → (row, col)` dict once
2. **"Starts at 'a'" trick:** prepend `'a'` to s → becomes pure adjacent-pair aggregation (same template as LC 3173, 3110)
3. **Manhattan distance** = 2D version of `abs(ord(a) - ord(b))` from LC 3110

---

## LAYER 1: Line-by-Line Explanation

**Preferred — build map, sum pairwise distances:**

```python
class Solution:
    def totalDistance(self, s: str) -> int:
        # Step 1: Define the keyboard layout (3 rows, variable widths)
        keyboard = [
            "qwertyuiop",   # Row 0
            "asdfghjkl",    # Row 1 (starts with 'a')
            "zxcvbnm"       # Row 2
        ]
        
        # Step 2: Map each char to (row, col) — O(26) one-time
        pos = {}                                # dict: char → (row, col)
        for r, row in enumerate(keyboard):
            for c, ch in enumerate(row):
                pos[ch] = (r, c)
        
        # Step 3: Walk the string, sum Manhattan distances
        total = 0                               # Running sum of distances
        prev = pos['a']                         # Finger starts at 'a' = (1, 0)
        
        for ch in s:
            r1, c1 = prev                       # Current finger position
            r2, c2 = pos[ch]                    # Target key position
            total += abs(r1 - r2) + abs(c1 - c2)  # Manhattan distance
            prev = (r2, c2)                     # Move finger to new key
        
        return total
```

**Why store positions as dict:** Rows have different lengths (10, 9, 7), so simple arithmetic like `(ord(c)-ord('a')) // 10` doesn't work. Build the map once; O(1) lookups thereafter.

**Pythonic one-liner with "prepend 'a'" trick:**

```python
class Solution:
    def totalDistance(self, s: str) -> int:
        keyboard = ["qwertyuiop", "asdfghjkl", "zxcvbnm"]
        # Dict comp builds char → (row, col) in one expression
        pos = {ch: (r, c) for r, row in enumerate(keyboard)
                          for c, ch in enumerate(row)}
        
        # Prepend 'a' to make starting position part of the sequence
        # → Same adjacent-pair aggregation as LC 3110, 3173
        path = 'a' + s
        
        return sum(
            abs(pos[a][0] - pos[b][0]) + abs(pos[a][1] - pos[b][1])
            for a, b in zip(path, path[1:])
        )
```

The `'a' + s` is the trick — turns "finger starts at 'a', then visits each char" into "visit every adjacent pair in path."

**Cleaner with helper function:**

```python
class Solution:
    def totalDistance(self, s: str) -> int:
        keyboard = ["qwertyuiop", "asdfghjkl", "zxcvbnm"]
        pos = {ch: (r, c) for r, row in enumerate(keyboard)
                          for c, ch in enumerate(row)}
        
        def dist(a: str, b: str) -> int:
            """Manhattan distance between two keys."""
            r1, c1 = pos[a]
            r2, c2 = pos[b]
            return abs(r1 - r2) + abs(c1 - c2)
        
        path = 'a' + s
        return sum(dist(a, b) for a, b in zip(path, path[1:]))
```

---

## LAYER 2: Worked Examples

**Example 1: `s = "hello"` → 17**

Position map (excerpt):

| char | (r, c) |
|------|--------|
| a | (1, 0) |
| e | (0, 2) |
| h | (1, 5) |
| l | (1, 8) |
| o | (0, 8) |

Trace each move from `'a'`:

| move | from | to | dist |
|------|------|-----|------|
| a → h | (1, 0) | (1, 5) | 0 + 5 = 5 |
| h → e | (1, 5) | (0, 2) | 1 + 3 = 4 |
| e → l | (0, 2) | (1, 8) | 1 + 6 = 7 |
| l → l | (1, 8) | (1, 8) | 0 + 0 = 0 |
| l → o | (1, 8) | (0, 8) | 1 + 0 = 1 |

Total: 5 + 4 + 7 + 0 + 1 = **17** ✓

**Example 2: `s = "a"` → 0**

Only target is 'a'; finger already there. Distance 0. ✓

**The `'a' + s` trick on Example 1:**

```
path = "ahello"
zip yields: ('a','h'), ('h','e'), ('e','l'), ('l','l'), ('l','o')
distances: 5, 4, 7, 0, 1
sum = 17 ✓
```

**Edge cases:**
- Length 1, target = 'a': 0 (no move)
- Length 1, target ≠ 'a': single Manhattan distance from (1,0)
- All same char: `"aaaa"` → 0
- Long s up to 10⁴: O(n) → fine
- Same row: only column distance accumulates
- Same column: only row distance

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Dict lookup + loop | O(n) after O(26) setup | O(26) | **Default, optimal** |
| `'a' + s` + zip | O(n) | O(26 + n) | **Most Pythonic** |
| Grid scan per char | O(n × 26) | O(1) | Wasteful |

**Why this is Medium:**

Three composed steps:
1. Parse the spec into a usable structure
2. Recognize the path-pairwise pattern
3. Handle the implicit "starts at 'a'"

Each is easy. Composition is what makes it Medium.

**The "implicit starting state" trick:**

Two ways to handle implicit start:
1. **Prepend start to sequence:** uniform pairwise loop
2. **Initialize state explicitly:** more general (works for streaming)

**Manhattan distance — where else:**

`|x1 − x2| + |y1 − y2|` is "taxi-cab geometry" — only horizontal/vertical moves allowed. Appears in:
- Grid pathfinding
- Image processing (city-block distance)
- Robot motion
- LC 3110 (1D version with just abs(ord diff))

For diagonal moves: **Chebyshev distance** `max(|x1−x2|, |y1−y2|)`.
For straight-line: **Euclidean** `sqrt(...)`.

**Why dict > grid-search per query:**

Grid search per char: O(26) × n = O(26n). Dict pre-builds inverse map: O(26) once, O(1) per lookup. Amortized win.

**The "lookup table" general pattern:**

Any small fixed mapping (chars → positions, types → costs, days → indices) → pre-build dict outside the main loop.

---

## LAYER 4: Interview Variations

• **Two fingers:** DP — minimum over which finger moves.
• **Custom keyboard layout as input:** Build map from input.
• **Different metric (Euclidean, Chebyshev):** Swap distance formula.
• **Exact path of cells visited:** Multiple shortest paths; return one canonical.
• **Min total over all permutations of s:** TSP-like (hard in general).
• **Broken keys:** Filter/remap.
• **Weighted keys:** Add landing cost.
• **Diagonal allowed:** `max(abs(r diff), abs(c diff))`.
• **Wrap-around (toroidal):** `min(diff, dim − diff)` per axis.
• **Streaming chars:** Maintain finger; Manhattan per char.
• **Squared distance:** Replace formula.
• **Multiple users typing:** Independent finger states.
• **Worst single transition (max jump):** Replace `sum` with `max`.
• **All distances per transition:** `itertools.accumulate`.
• **Parameterized start char:** `prev = pos[start_char]`.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — explicit state, no prepend
class Solution:
    def totalDistance(self, s: str) -> int:
        keyboard = ["qwertyuiop", "asdfghjkl", "zxcvbnm"]
        pos = {ch: (r, c) for r, row in enumerate(keyboard)
                          for c, ch in enumerate(row)}
        
        total = 0
        r, c = 1, 0                             # Start at 'a'
        for ch in s:
            nr, nc = pos[ch]
            total += abs(r - nr) + abs(c - nc)
            r, c = nr, nc
        return total
```

**Pythonic — prepend 'a' + zip pairs:**
```python
def totalDistance(s):
    keyboard = ["qwertyuiop", "asdfghjkl", "zxcvbnm"]
    pos = {ch: (r, c) for r, row in enumerate(keyboard)
                      for c, ch in enumerate(row)}
    path = 'a' + s
    return sum(
        abs(pos[a][0] - pos[b][0]) + abs(pos[a][1] - pos[b][1])
        for a, b in zip(path, path[1:])
    )
```

**With helper function:**
```python
def totalDistance(s):
    keyboard = ["qwertyuiop", "asdfghjkl", "zxcvbnm"]
    pos = {ch: (r, c) for r, row in enumerate(keyboard)
                      for c, ch in enumerate(row)}
    
    def dist(a, b):
        r1, c1 = pos[a]
        r2, c2 = pos[b]
        return abs(r1 - r2) + abs(c1 - c2)
    
    path = 'a' + s
    return sum(dist(a, b) for a, b in zip(path, path[1:]))
```

---

**Time:** O(n) | **Space:** O(26) for position map

**Pattern flag:** "Process a sequence with implicit starting state, summing pairwise costs" → (1) prepend start to make uniform pairwise, or (2) maintain explicit running state. Combined with **Manhattan distance over a fixed lookup**, this is "build map once, sum the deltas." Applies to ANY problem with a fixed coordinate system + sequence of visits.
