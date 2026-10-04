# 3289. The Two Sneaky Numbers of Digitville

**Difficulty:** Easy
**Pattern:** Hash Table / Frequency Counter / Increment-Then-Check
**Date Solved:** 2026-06-07
**Status:** ✅

---

## Understanding the Goal

Array has `n + 2` integers in range `[0, n-1]`. Two of them appear **twice**; the rest once. Find the two duplicates.

**Key insight:** Count occurrences. Whichever reach count 2 are the sneaky ones.

The editorial uses **increment-then-check** — same pattern as LC 2657 (Prefix Common Array). When the count hits exactly 2, append immediately. Avoids a second pass.

---

## LAYER 1: Line-by-Line Explanation

### Editorial — dict with increment-then-check

```python
class Solution:
    def getSneakyNumbers(self, nums: List[int]) -> List[int]:
        res = []                                # Holds the two duplicates
        count = {}                              # Dict: number → times seen
        
        for x in nums:
            # `count.get(x, 0)` returns 0 if x never seen (safe init)
            # Add 1 and write back
            count[x] = count.get(x, 0) + 1
            
            # The MOMENT count hits 2 → this is a duplicate → record
            # We catch it ONCE (when count crosses from 1 to 2)
            if count[x] == 2:
                res.append(x)
        
        return res
```

**Why `count.get(x, 0) + 1`:** For a brand-new key, `count[x]` raises `KeyError`. `.get(x, 0)` returns 0 instead — safe initialization.

**Why check `== 2` (not `>= 2`):** Fires exactly once when count crosses 1 → 2. Problem guarantees each duplicate appears exactly twice, but `== 2` is the safer, more precise check.

### Pythonic with Counter

```python
from collections import Counter

class Solution:
    def getSneakyNumbers(self, nums: List[int]) -> List[int]:
        # Counter builds {num: count} in one line
        # Filter to numbers appearing twice
        counts = Counter(nums)
        return [num for num, c in counts.items() if c == 2]
```

Two-pass tradeoff: builds full counter first, then filters. Same O(n) time, more declarative.

### Set-based "seen first"

```python
class Solution:
    def getSneakyNumbers(self, nums: List[int]) -> List[int]:
        seen = set()                            # Numbers seen at least once
        res = []
        for x in nums:
            if x in seen:                       # Second time seeing x
                res.append(x)
            else:
                seen.add(x)                     # First time
        return res
```

Each duplicate appears exactly twice. FIRST time → add to `seen`. SECOND time → `x in seen` is True → append.

Single set (less memory than full counter), arguably cleaner since we only care about first vs second seen.

### Math trick (O(1) space — overkill but elegant)

```python
class Solution:
    def getSneakyNumbers(self, nums: List[int]) -> List[int]:
        n = len(nums) - 2                       # Expected range [0, n-1]
        expected_sum = n * (n - 1) // 2
        expected_sumsq = sum(i*i for i in range(n))
        
        # Let duplicates be a, b
        # actual_sum = expected_sum + a + b
        # actual_sumsq = expected_sumsq + a² + b²
        s = sum(nums) - expected_sum            # = a + b
        sq = sum(x*x for x in nums) - expected_sumsq  # = a² + b²
        
        # Solve quadratic: a + b = s, a*b = (s² - sq) / 2
        product = (s * s - sq) // 2
        disc = int((s * s - 4 * product) ** 0.5)
        a = (s + disc) // 2
        b = s - a
        return [a, b]
```

Mathematically clean but easy to mess up (overflow in C++/Java, off-by-one). Mention as an option in interviews; write the hash version.

---

## LAYER 2: Worked Examples

### Example 1: `nums = [0, 1, 1, 0]` → `[1, 0]`

| x | count before | count[x] after | == 2? | res |
|---|--------------|----------------|-------|-----|
| 0 | {} | 1 | no | [] |
| 1 | {0: 1} | 1 | no | [] |
| 1 | {0: 1, 1: 1} | 2 | **yes** | [1] |
| 0 | {0: 1, 1: 2} | 2 | **yes** | [1, 0] |

Result: `[1, 0]` ✓ (any order valid)

### Example 2: `nums = [0, 3, 2, 1, 3, 2]` → `[3, 2]`

| x | count[x] after | == 2? | res |
|---|----------------|-------|-----|
| 0 | 1 | no | [] |
| 3 | 1 | no | [] |
| 2 | 1 | no | [] |
| 1 | 1 | no | [] |
| 3 | 2 | yes | [3] |
| 2 | 2 | yes | [3, 2] |

Result: `[3, 2]` ✓

### Example 3: `nums = [7, 1, 5, 4, 3, 4, 6, 0, 9, 5, 8, 2]` → `[4, 5]`

- `4` first at index 3, second at index 5 → appended first
- `5` first at index 2, second at index 9 → appended second

Result: `[4, 5]` ✓

### Set-based trace on Example 1

```
seen = {}, res = []
x=0: 0 not in seen → seen={0}
x=1: 1 not in seen → seen={0, 1}
x=1: 1 in seen → res=[1]
x=0: 0 in seen → res=[1, 0]
```

### Edge cases
- Min n=2: array length 4, two unique numbers each twice
- Duplicates adjacent: caught immediately on second pass
- Duplicates far apart: both still caught

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Dict + increment-then-check | O(n) | O(n) | **Editorial; explicit** |
| Counter + filter | O(n) | O(n) | **Pythonic** |
| Set "seen first" | O(n) | O(n) | Slightly cleaner |
| Math (sum + sumsq) | O(n) | O(1) | Constant space |
| Bitmask of seen | O(n) | O(1) for n ≤ 64 | Bit-set version |

**The increment-then-check pattern (third encounter):**

```python
counter[key] += 1
if counter[key] == THRESHOLD:
    take_action(key)
```

Used in:
- **LC 2657 (Day 8) Prefix Common Array** — counter hits 2 → "value common to both prefixes"
- **LC 3289 (this) Sneaky Numbers** — counter hits 2 → "duplicate"
- LRU cache eviction — counter hits 0 → "evict"
- Reference counting — counter hits 0 → "free memory"

When you see "process a stream and react when count threshold is crossed," this is the idiom.

**Why "seen set" simpler than full counter:**

If you only need to detect duplicates (not count):
- "Is this a repeat?" → set
- "How many times?" → counter
- "Action exactly once at count K?" → increment-then-check

Choosing the lightest data structure that solves the problem is real engineering skill.

**Bitmask version (small value range):**

```python
class Solution:
    def getSneakyNumbers(self, nums: List[int]) -> List[int]:
        seen = 0                                # Bit set of seen indices
        res = []
        for x in nums:
            if seen >> x & 1:                   # Bit x set → already seen
                res.append(x)
            else:
                seen |= 1 << x                  # Set bit x
        return res
```

This is the bitmask-as-set trick from LC 1684. Python handles unbounded ints, so technically unlimited.

**Why math trick is interesting but not practical:**

- **Pro:** Demonstrates math chops
- **Con:** Integer overflow risk in C++/Java; off-by-one in expected sums
- **Verdict:** Mention; write hash version

**Difference from LC 287 (Find Duplicate Number):**

LC 287 has ONE duplicate in n+1 elements from [1, n], asks for O(1) space → Floyd's cycle detection (treating array as a function).

This problem has TWO duplicates, allows O(n) space → hash is fine.

---

## LAYER 4: Interview Variations

• **Find ONE duplicate (LC 287):** Floyd's tortoise-and-hare in O(1) space.
• **Find all duplicates (LC 442):** Mark indices by negating; multi-duplicate version.
• **Find K duplicates:** Hash counter; collect keys with count ≥ 2.
• **Find missing AND duplicate (LC 645):** XOR or math (sum + sumsq).
• **Find two missing:** Inverse problem; collect numbers with count 0.
• **Duplicates in a stream:** Bloom filter (probabilistic).
• **Limited memory (n=10⁹):** Bloom filter or external sort.
• **Single non-duplicate (LC 136):** XOR all; duplicates cancel out.
• **Two non-duplicates (LC 260):** XOR all, then partition by a set bit.
• **First duplicate (smallest index of second occurrence):** Return early.
• **Most frequent duplicate:** Counter, `max(items, key=lambda kv: kv[1])`.
• **Duplicates in sorted array:** Two-pointer or `arr[i] == arr[i+1]` check.
• **Duplicates within K distance (LC 219):** Sliding window of size K with hash set.
• **More than once:** `[k for k, v in Counter(arr).items() if v > 1]`.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — Pythonic with Counter
from collections import Counter

class Solution:
    def getSneakyNumbers(self, nums: List[int]) -> List[int]:
        return [num for num, c in Counter(nums).items() if c == 2]
```

**Editorial — increment-then-check:**
```python
class Solution:
    def getSneakyNumbers(self, nums):
        res = []
        count = {}
        for x in nums:
            count[x] = count.get(x, 0) + 1
            if count[x] == 2:
                res.append(x)
        return res
```

**Set-based "seen first":**
```python
def getSneakyNumbers(nums):
    seen = set()
    res = []
    for x in nums:
        if x in seen:
            res.append(x)
        else:
            seen.add(x)
    return res
```

**Bitmask version:**
```python
def getSneakyNumbers(nums):
    seen = 0
    res = []
    for x in nums:
        if (seen >> x) & 1:
            res.append(x)
        else:
            seen |= 1 << x
    return res
```

**Math version (O(1) space):**
```python
def getSneakyNumbers(nums):
    n = len(nums) - 2
    exp_sum = n * (n - 1) // 2
    exp_sq = sum(i*i for i in range(n))
    s = sum(nums) - exp_sum
    sq = sum(x*x for x in nums) - exp_sq
    product = (s * s - sq) // 2
    disc = int((s * s - 4 * product) ** 0.5)
    a = (s + disc) // 2
    b = s - a
    return [a, b]
```

**defaultdict version:**
```python
from collections import defaultdict

def getSneakyNumbers(nums):
    count = defaultdict(int)
    res = []
    for x in nums:
        count[x] += 1
        if count[x] == 2:
            res.append(x)
    return res
```

---

**Time:** O(n) | **Space:** O(n) for hash, O(1) for math

**Pattern flag:** "Find elements appearing K times in a stream" → **increment-then-check** with counter. When K=2 (find duplicates), set of "seen" can substitute. When value range is small and fixed, bitmask as set is even tighter. Same pattern as LC 2657 — the "counter hits N → take action" idiom is one of the most reusable in counting algorithms.
