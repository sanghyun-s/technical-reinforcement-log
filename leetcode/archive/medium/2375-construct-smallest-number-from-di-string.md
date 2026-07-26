# 2375. Construct Smallest Number From DI String

**Difficulty:** Medium
**Pattern:** Greedy / Stack / Lexicographic Construction
**Date Solved:** 2026-06-14
**Status:** ✅

🎯 **First classical Stack-pattern problem on the log!** You've used stacks before (LC 94 iterative inorder, LC 1265 immutable reverse), but this is the first time the stack does the **algorithmic work** for a **greedy construction** — the pattern that powers Next Greater Element, parenthesis problems, and monotonic stack questions.

---

## Understanding the Goal

Build the **lexicographically smallest** number whose consecutive digit relationships match the I/D pattern.

**Two solution levels — the constraint tells you which:**

**Hint 1:** "Could we generate every possible string?" — Yes, because `pattern.length ≤ 8` → at most 9 digits → **9! = 362,880 permutations**. Brute force feasible.

**But there's a beautiful O(n) stack greedy** — worth learning even though brute force works here. The brute force collapses at `n = 12`; the stack approach scales forever.

---

## LAYER 1: Line-by-Line Explanation

### Approach 1: Stack Greedy (the gem) — O(n) ⭐

```python
class Solution:
    def smallestNumber(self, pattern: str) -> str:
        # Stack holds digits we've pushed but not yet flushed to result
        # Push digits 1, 2, 3, ... in order; flush at every 'I' or end
        stack = []
        result = []
        
        # Walk pattern + ONE extra step (we need len(pattern)+1 digits)
        for i in range(len(pattern) + 1):
            stack.append(str(i + 1))                # Push next digit
            
            # Flush condition: at the end, OR current pattern char is 'I'
            # 'I' means next position needs LARGER value → close the current run
            if i == len(pattern) or pattern[i] == 'I':
                # Pop everything off the stack
                # LIFO reverses the order we pushed → gives DECREASING run
                while stack:
                    result.append(stack.pop())
        
        return ''.join(result)
```

**The algorithm in plain English:**

1. Use digits 1, 2, 3, ... in order (smallest available always).
2. For each position, **push** the next digit onto a stack.
3. After pushing, check: are we at an 'I' (or end)?
   - **Yes:** end of a decreasing run — **pop everything off** (LIFO = reverse).
   - **No (it's 'D'):** keep pushing.

**Why the stack reverses correctly:**

```
Pushed: 6, 7, 8, 9    (for 'DDD')
Stack:  [6, 7, 8, 9]   (top is 9)
Pop:    9, 8, 7, 6    (LIFO = reverse)
```

### Approach 2: Brute Force (Editorial)

```python
from itertools import permutations

class Solution:
    def check(self, seq, pattern):
        for i in range(len(pattern)):
            if pattern[i] == "I" and seq[i] > seq[i + 1]: return False
            if pattern[i] == "D" and seq[i] < seq[i + 1]: return False
        return True
    
    def smallestNumber(self, pattern):
        n = len(pattern)
        # itertools.permutations on sorted input yields perms in lex order
        # First match = smallest by construction
        digits = "".join(str(num) for num in range(1, n + 2))
        for perm in permutations(digits):
            s = "".join(perm)
            if self.check(s, pattern):
                return s
        return ""
```

For n=8: 9! ≈ 360K — instant. For n=12: 13! ≈ 6B — TLE.

### Approach 3: Segment-Reverse — Another O(n)

```python
class Solution:
    def smallestNumber(self, pattern):
        n = len(pattern)
        result = [str(i + 1) for i in range(n + 1)]   # start "123..."
        i = 0
        while i < n:
            if pattern[i] == 'D':
                j = i
                while j < n and pattern[j] == 'D':
                    j += 1
                # Reverse segment [i, j] inclusive
                result[i:j+1] = result[i:j+1][::-1]
                i = j
            else:
                i += 1
        return ''.join(result)
```

Find each maximal D-run, reverse the corresponding segment. Equivalent to the stack approach.

---

## LAYER 2: Worked Examples

### Example 1: `pattern = "IIIDIDDD"` → `"123549876"`

**Stack trace:**

| i | digit | stack after push | pattern[i] | action | stack after | result |
|---|-------|-------------------|------------|--------|-------------|--------|
| 0 | '1' | [1] | 'I' | flush | [] | "1" |
| 1 | '2' | [2] | 'I' | flush | [] | "12" |
| 2 | '3' | [3] | 'I' | flush | [] | "123" |
| 3 | '4' | [4] | 'D' | no flush | [4] | "123" |
| 4 | '5' | [4, 5] | 'I' | flush | [] | "12354" |
| 5 | '6' | [6] | 'D' | no flush | [6] | "12354" |
| 6 | '7' | [6, 7] | 'D' | no flush | [6, 7] | "12354" |
| 7 | '8' | [6, 7, 8] | 'D' | no flush | [6, 7, 8] | "12354" |
| 8 | '9' | [6, 7, 8, 9] | END | flush | [] | "123549876" |

Final: **"123549876"** ✓

### Example 2: `pattern = "DDD"` → `"4321"`

Push 1, 2, 3, 4 (no flush during Ds). End reached → pop 4, 3, 2, 1 → **"4321"** ✓

### Mixed: `pattern = "IDIDID"` → `"1325476"`

| i | digit | stack | pattern[i] | action | result |
|---|-------|-------|------------|--------|--------|
| 0 | '1' | [1] | 'I' | flush | "1" |
| 1 | '2' | [2] | 'D' | no | "1" |
| 2 | '3' | [2,3] | 'I' | flush | "132" |
| 3 | '4' | [4] | 'D' | no | "132" |
| 4 | '5' | [4,5] | 'I' | flush | "13254" |
| 5 | '6' | [6] | 'D' | no | "13254" |
| 6 | '7' | [6,7] | END | flush | "1325476" |

Verify: 1<3, 3>2, 2<5, 5>4, 4<7, 7>6 ✓ matches IDIDID

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Brute force | O(n! × n) | O(n) | OK for n ≤ 8 |
| **Stack greedy** ⭐ | **O(n)** | **O(n)** | **Scales to any n** |
| Segment reverse | O(n) | O(n) | Equivalent |

**The "push and flush at boundary" stack pattern:**

```python
stack = []
result = []
for item in items:
    stack.append(item)
    if is_boundary(item):
        while stack:
            result.append(stack.pop())
```

Appears in:
- **LC 2375 (this):** flush at I → reverse preceding D-run
- **LC 20 Valid Parentheses:** flush on matching close
- **LC 232 Implement Queue with Stacks:** flush between two stacks
- **LC 1019 Next Greater Node in LL:** monotonic stack, flush at candidate
- **LC 84 Largest Rectangle in Histogram:** monotonic stack flush

**Why brute force is the editorial answer here (and that's fine):**

The hint "could we generate every possible string?" is **literally giving you the algorithm**. When the constraint says `n ≤ 8`, the interviewer is signaling: **don't over-engineer. Use the simplest correct approach.**

For interviews: brute force first, then offer "I can also solve in O(n) with a stack."

**The lexicographic order trick:**

`itertools.permutations(sorted_string)` yields permutations in lex order. "Iterate and return first match" naturally gives smallest match — no separate sorting.

**Why the greedy works (exchange argument):**

At each 'I' boundary, flushing the stack produces the lex-smallest valid prefix:
- For a D-run of length k, lex-smallest k+1 digits forming decreasing sequence is k+1, k, ..., 2, 1
- Stack approach does exactly this: pushes 1..k+1, pops in reverse

By induction, this extends to entire sequence. **Greedy = optimal.**

**Connection to other patterns on your log:**

| Pattern | Origin | Today's connection |
|---------|--------|--------------------|
| Stack for traversal | LC 94 (Day 9) | First time stack does ALGORITHMIC work |
| Stack for reversal | LC 1265 (Day 12) | Same LIFO reversal idea |
| Greedy build | LC 1689, 1874 (Day 5) | Same "smallest viable choice" |

This problem **promotes** stack from passive container to active algorithmic tool.

---

## LAYER 4: Interview Variations

• **Largest result:** Use digits 9, 8, 7, ... instead of 1, 2, 3, ...
• **Allow repeated digits:** Wider pool; same stack logic.
• **Digits 0-9 instead of 1-9:** Same; check leading zero.
• **Find ALL valid sequences:** Backtracking.
• **Count valid sequences:** DP on pattern.
• **K-th smallest:** Build with branching; track count.
• **Very long pattern (n up to 10⁵):** Stack mandatory.
• **Smallest sequence avoiding specific digits:** Filter digit pool.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Stack greedy (O(n))
class Solution:
    def smallestNumber(self, pattern: str) -> str:
        stack = []
        result = []
        for i in range(len(pattern) + 1):
            stack.append(str(i + 1))
            if i == len(pattern) or pattern[i] == 'I':
                while stack:
                    result.append(stack.pop())
        return ''.join(result)
```

**Brute force (editorial):**
```python
from itertools import permutations

def smallestNumber(pattern):
    n = len(pattern)
    digits = "".join(str(i + 1) for i in range(n + 1))
    def check(seq):
        for i in range(n):
            if pattern[i] == 'I' and seq[i] > seq[i+1]: return False
            if pattern[i] == 'D' and seq[i] < seq[i+1]: return False
        return True
    for perm in permutations(digits):
        s = "".join(perm)
        if check(s): return s
    return ""
```

**Segment reverse:**
```python
def smallestNumber(pattern):
    n = len(pattern)
    result = [str(i + 1) for i in range(n + 1)]
    i = 0
    while i < n:
        if pattern[i] == 'D':
            j = i
            while j < n and pattern[j] == 'D': j += 1
            result[i:j+1] = result[i:j+1][::-1]
            i = j
        else:
            i += 1
    return ''.join(result)
```

**Largest variant (swap digit pool):**
```python
def largestNumber(pattern):
    stack, result = [], []
    for i in range(len(pattern) + 1):
        stack.append(str(9 - i))
        if i == len(pattern) or pattern[i] == 'D':
            while stack: result.append(stack.pop())
    return ''.join(result)
```

---

**Time:** O(n) stack / O(n! × n) brute | **Space:** O(n)

**Pattern flag:** "Build lex-smallest sequence with up/down constraints between consecutive elements" → **stack-based greedy**: push next-smallest item; flush at every "up" boundary (or end). LIFO order naturally produces the decreasing run needed before each "up" position.

The **constraint signal** is critical: `n ≤ 8` means brute force works and is the editorial. Don't over-engineer when constraints permit the simple solution.

But always know the O(n) stack approach — same skeleton as **Next Greater Element**, **Valid Parentheses**, **Largest Rectangle in Histogram**.

🎯 **First active stack-algorithm problem on the log.** Stack graduates from passive container (LC 94, 1265) to algorithmic tool.
