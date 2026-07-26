# 3450. Maximum Students on a Single Bench

**Difficulty:** Easy
**Pattern:** Hash Table / Group-By / Set per Group
**Date Solved:** 2026-06-09
**Status:** ✅

---

## Understanding the Goal

Given pairs of `(student_id, bench_id)`, find the bench with the most **unique** students. Duplicate (student, bench) pairs count once.

**Key insight:** Three sub-steps:
1. **Group** students by bench — `defaultdict(set)` is perfect
2. **Deduplicate** within each bench — set add is idempotent
3. **Take max** of group sizes — with `default=0` for empty case

Hint says "hash table to group by bench ID." The natural Python implementation uses `defaultdict(set)`.

---

## LAYER 1: Line-by-Line Explanation

### Pythonic — defaultdict of sets

```python
from collections import defaultdict

class Solution:
    def maxStudentsOnBench(self, students: List[List[int]]) -> int:
        # defaultdict(set): keys = bench_ids, values = sets of student_ids
        # Missing key auto-creates an empty set
        bench_to_students = defaultdict(set)
        
        # Group: for each (student, bench), add student to that bench's set
        # set.add() is idempotent — duplicates silently ignored
        for student_id, bench_id in students:
            bench_to_students[bench_id].add(student_id)
        
        # Max size across benches; default=0 handles empty input
        return max((len(s) for s in bench_to_students.values()), default=0)
```

**Why `defaultdict(set)` over plain `dict`:**

Plain dict requires:
```python
if bench_id not in bench_to_students:
    bench_to_students[bench_id] = set()
bench_to_students[bench_id].add(student_id)
```

`defaultdict(set)` collapses to one line. Missing key auto-creates empty set.

**Why `max(..., default=0)`:**

If `students = []`, dict stays empty, `max(...)` on empty raises `ValueError`. `default=0` handles cleanly — same idiom as LC 3541 (most-frequent-vowel) on Day 10.

### Explicit loop (most readable)

```python
from collections import defaultdict

class Solution:
    def maxStudentsOnBench(self, students: List[List[int]]) -> int:
        bench_to_students = defaultdict(set)
        for student_id, bench_id in students:
            bench_to_students[bench_id].add(student_id)
        
        max_count = 0
        for student_set in bench_to_students.values():
            max_count = max(max_count, len(student_set))
        return max_count
```

### Without defaultdict (manual setdefault)

```python
class Solution:
    def maxStudentsOnBench(self, students: List[List[int]]) -> int:
        bench_to_students = {}
        for student_id, bench_id in students:
            # setdefault returns value at key, creating with default if missing
            bench_to_students.setdefault(bench_id, set()).add(student_id)
        return max((len(s) for s in bench_to_students.values()), default=0)
```

`dict.setdefault(key, default)` is the manual equivalent — no import needed.

### Pure set-of-tuples approach

```python
class Solution:
    def maxStudentsOnBench(self, students: List[List[int]]) -> int:
        # Step 1: dedupe at (student, bench) pair level
        unique_pairs = set(tuple(p) for p in students)
        
        # Step 2: count occurrences by bench
        bench_counts = {}
        for student_id, bench_id in unique_pairs:
            bench_counts[bench_id] = bench_counts.get(bench_id, 0) + 1
        
        return max(bench_counts.values(), default=0)
```

Different angle: dedupe first, then count.

---

## LAYER 2: Worked Examples

### Example 1: `[[1,2], [2,2], [3,3], [1,3], [2,3]]` → 3

| (student, bench) | bench_to_students after |
|------------------|--------------------------|
| (1, 2) | {2: {1}} |
| (2, 2) | {2: {1, 2}} |
| (3, 3) | {2: {1, 2}, 3: {3}} |
| (1, 3) | {2: {1, 2}, 3: {3, 1}} |
| (2, 3) | {2: {1, 2}, 3: {3, 1, 2}} |

Bench 2: 2 unique. Bench 3: 3 unique. Max: **3** ✓

### Example 2: `[[1,1], [2,1], [3,1], [4,2], [5,2]]` → 3

| bench | students |
|-------|----------|
| 1 | {1, 2, 3} → size 3 |
| 2 | {4, 5} → size 2 |

Max: **3** ✓

### Example 3: `[[1,1], [1,1]]` → 1

Same pair twice. Set: `{1: {1}}`. `set.add()` idempotent. Max: **1** ✓

### Example 4: `[]` → 0

Empty input. `max(<empty>, default=0)` → **0** ✓

### Edge cases

- **Empty:** Return 0 via `default=0`
- **One student, one bench:** 1
- **All on same bench:** n (distinct students)
- **All on different benches:** 1
- **Max n=100:** Trivially fast

---

## LAYER 3: Key Insights

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **defaultdict(set)** | **O(n)** | **O(n)** | **Preferred** |
| Manual dict + setdefault | O(n) | O(n) | No import |
| Set of tuples + count | O(n) | O(n) | Two-pass |
| Sort + groupby | O(n log n) | O(n) | Overkill |

**The "group-by + aggregate" pattern (most reused in data processing):**

> "Group items by some key; compute aggregate per group; take best."

Skeleton:
```python
groups = defaultdict(SOMETHING)         # set, list, int, Counter — by aggregation
for item in items:
    groups[item.key].SOMETHING(item.value)

result = AGGREGATE(SOMETHING(g) for g in groups.values())
```

Seen in:
- **LC 1512 (Day 6) Good Pairs:** group by value, count pairs
- **LC 3541 (Day 10) Vowels/Consonants:** group by category, find max freq
- **LC 3289 (Day 10) Sneaky Numbers:** group by value, find count == 2
- **LC 1255 (today) Words from Letters:** group letters per word
- **LC 3450 (this):** group by bench, find max set size

The **MapReduce primitive**. Everywhere in data pipelines, SQL (GROUP BY), pandas (`.groupby()`).

**Picking the right value type for defaultdict:**

| Goal | Type |
|------|------|
| Count distinct | `defaultdict(set)` — this problem |
| Count occurrences | `defaultdict(int)` |
| Collect items | `defaultdict(list)` |
| Sum values | `defaultdict(int)` |
| Multi-attribute counts | `defaultdict(Counter)` |
| Nested | `defaultdict(lambda: defaultdict(int))` |

**Why `set` is right here:**

"Counted only once per bench" = literal definition of a set. Using set:
1. Auto-handles deduplication
2. `len(set)` directly gives answer
3. `set.add()` idempotent — safe with duplicates

**Set vs Counter:**

Counter is overkill — only need distinct, not counts. Use lightest data structure that solves the problem.

**`default=0` idiom appearing again:**

Day 10 LC 3541, today LC 3541 + this. **Third encounter.** When you need "max of possibly-empty iterable with sentinel," `default=` is the clean answer.

**Connection to SQL — this IS `GROUP BY ... COUNT(DISTINCT)`:**

```sql
SELECT bench_id, COUNT(DISTINCT student_id) AS unique_students
FROM students
GROUP BY bench_id
ORDER BY unique_students DESC
LIMIT 1;
```

You solved exactly this in LC 2356 (Day 8). Same pattern, different language. **SQL ↔ Python correspondence is a real cross-domain skill.**

---

## LAYER 4: Interview Variations

• **Return BENCH ID (not count):** `max(bench_to_students.items(), key=lambda kv: len(kv[1]))[0]`.
• **All benches with max (ties):** Two-pass — find max, collect matches.
• **Top K benches:** `sorted(benches.items(), key=lambda kv: -len(kv[1]))[:K]`.
• **Average unique per bench:** `sum(len(s) for s in benches.values()) / len(benches)`.
• **Bench with most students (counting duplicates):** `defaultdict(int)` + increment.
• **Multiple benches per student:** Flip grouping: `defaultdict(set)` keyed by student.
• **Benches that share students:** Check intersections — `len(bench_a & bench_b)`.
• **Streaming students:** Maintain map incrementally.
• **Capacity constraint:** Reject adds when size exceeds limit.
• **Histogram of bench sizes:** `Counter(len(s) for s in benches.values())`.
• **Two-bench query "A but not B":** Set difference: `benches[a] - benches[b]`.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — defaultdict of sets
from collections import defaultdict

class Solution:
    def maxStudentsOnBench(self, students: List[List[int]]) -> int:
        bench_to_students = defaultdict(set)
        for student_id, bench_id in students:
            bench_to_students[bench_id].add(student_id)
        return max((len(s) for s in bench_to_students.values()), default=0)
```

**Explicit loop:**
```python
from collections import defaultdict

def maxStudentsOnBench(students):
    bench_to_students = defaultdict(set)
    for student_id, bench_id in students:
        bench_to_students[bench_id].add(student_id)
    max_count = 0
    for student_set in bench_to_students.values():
        max_count = max(max_count, len(student_set))
    return max_count
```

**Manual dict with setdefault:**
```python
def maxStudentsOnBench(students):
    bench_to_students = {}
    for student_id, bench_id in students:
        bench_to_students.setdefault(bench_id, set()).add(student_id)
    return max((len(s) for s in bench_to_students.values()), default=0)
```

**Set of tuples + count:**
```python
def maxStudentsOnBench(students):
    unique_pairs = set(tuple(p) for p in students)
    bench_counts = {}
    for student_id, bench_id in unique_pairs:
        bench_counts[bench_id] = bench_counts.get(bench_id, 0) + 1
    return max(bench_counts.values(), default=0)
```

**Counter on bench IDs (after deduping pairs):**
```python
from collections import Counter

def maxStudentsOnBench(students):
    if not students:
        return 0
    unique_pairs = {tuple(p) for p in students}
    return max(Counter(b for _, b in unique_pairs).values())
```

---

**Time:** O(n) | **Space:** O(n)

**Pattern flag:** "Group items by key; compute aggregate per group" → **`defaultdict(...)`** with the right value type. For DISTINCT counting, use `defaultdict(set)` — `set.add` idempotent, `len(set)` gives answer.

**Python equivalent of SQL `GROUP BY ... COUNT(DISTINCT)`**. You've done this in SQL (LC 2356) — now in Python. Recognizing cross-domain patterns is worth more than any single solution. The "group-by + aggregate" framework powers MapReduce, SQL aggregations, pandas `.groupby()`.

The `max(..., default=0)` idiom — third encounter on the log. **Canonical Python form** for "best of possibly-empty iterable with sentinel."
