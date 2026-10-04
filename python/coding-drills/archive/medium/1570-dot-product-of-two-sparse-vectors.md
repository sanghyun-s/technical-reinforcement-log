# 1570. Dot Product of Two Sparse Vectors

**Difficulty:** Medium
**Pattern:** Hash Table / Sparse Representation / Class Design
**Date Solved:** 2026-06-07
**Status:** ✅

---

## Understanding the Goal

Compute dot product of two vectors that are "mostly zeros." Dense approach `sum(a*b for a, b in zip(v1, v2))` wastes time on zero terms.

**Key insight:** Skip zeros entirely. Only nonzero entries contribute (`0 × anything = 0`). Store each vector as a **hash map `{index: value}` of nonzero entries**, then for each nonzero index in one vector, check if the other has it.

First **class design** problem on log — implementing `__init__` + `dotProduct` API.

The follow-up ("What if only one is sparse?") is what makes this Medium.

---

## LAYER 1: Line-by-Line Explanation

### Editorial — hash map of nonzero indices

```python
class SparseVector:
    def __init__(self, nums: List[int]):
        # Store only nonzero entries: {index: value}
        # For [1, 0, 0, 2, 3], this builds {0: 1, 3: 2, 4: 3}
        # Skips zeros entirely — the whole point of "sparse"
        self.nonzeros = {}
        for i, n in enumerate(nums):
            if n != 0:
                self.nonzeros[i] = n
    
    def dotProduct(self, vec: 'SparseVector') -> int:
        result = 0
        # Iterate THIS vector's nonzero entries
        # For each (i, n), check if the OTHER vector has nonzero at i
        # If both nonzero → multiply + accumulate
        # If other has 0 at i (i not in vec.nonzeros) → skip (would contribute 0)
        for i, n in self.nonzeros.items():
            if i in vec.nonzeros:                # O(1) hash lookup
                result += n * vec.nonzeros[i]
        return result
```

**Reading the design:**
- `__init__` does one-time O(n) sweep to extract nonzeros into a dict
- `dotProduct` iterates only nonzeros — if v1 has K nonzeros and v2 has L, at most O(min(K, L)) work

**Why hash map (not list of tuples):** With a dict, `i in vec.nonzeros` is O(1) on average. With a list, it would be O(K) scan.

### Slightly optimized — iterate the smaller dict

```python
class SparseVector:
    def __init__(self, nums: List[int]):
        # Dict comprehension — same result, more Pythonic
        self.nonzeros = {i: n for i, n in enumerate(nums) if n != 0}
    
    def dotProduct(self, vec: 'SparseVector') -> int:
        # Pick smaller dict to iterate (saves work when sizes differ)
        a, b = self.nonzeros, vec.nonzeros
        if len(a) > len(b):
            a, b = b, a                          # Swap so a is smaller
        
        result = 0
        for i, val in a.items():
            if i in b:                           # O(1) hash lookup
                result += val * b[i]
        return result
```

### One-liner with set intersection

```python
class SparseVector:
    def __init__(self, nums):
        self.nonzeros = {i: n for i, n in enumerate(nums) if n != 0}
    
    def dotProduct(self, vec):
        # Common indices = intersection of two key sets
        common = self.nonzeros.keys() & vec.nonzeros.keys()
        return sum(self.nonzeros[i] * vec.nonzeros[i] for i in common)
```

`dict.keys()` returns a view supporting set operations. `&` gives indices where BOTH are nonzero.

### Alternative — two-pointer on sorted tuples

```python
class SparseVector:
    def __init__(self, nums):
        # Sorted list of (index, value) pairs
        self.nonzeros = [(i, n) for i, n in enumerate(nums) if n != 0]
    
    def dotProduct(self, vec):
        result = 0
        i, j = 0, 0                              # Pointers into self and vec
        while i < len(self.nonzeros) and j < len(vec.nonzeros):
            idx_a, val_a = self.nonzeros[i]
            idx_b, val_b = vec.nonzeros[j]
            
            if idx_a == idx_b:                   # Both have this index
                result += val_a * val_b
                i += 1
                j += 1
            elif idx_a < idx_b:                  # self's index smaller; advance self
                i += 1
            else:
                j += 1                            # vec's index smaller; advance vec
        return result
```

Two-pointer is preferred when: no hash maps available, want O(1) extra space per query, or memory locality matters.

---

## LAYER 2: Worked Examples

### Example 1: `nums1 = [1,0,0,2,3]`, `nums2 = [0,3,0,4,0]` → 8

After `__init__`:
- `v1.nonzeros = {0: 1, 3: 2, 4: 3}`
- `v2.nonzeros = {1: 3, 3: 4}`

Iterating v1:

| i | v1.nonzeros[i] | i in v2? | v2.nonzeros[i] | contribution |
|---|----------------|----------|----------------|--------------|
| 0 | 1 | No | — | 0 |
| 3 | 2 | Yes | 4 | 8 |
| 4 | 3 | No | — | 0 |

Total: **8** ✓

Verifying dense: `1*0 + 0*3 + 0*0 + 2*4 + 3*0 = 8` ✓

### Example 2: `nums1 = [0,1,0,0,0]`, `nums2 = [0,0,0,0,2]` → 0

- `v1.nonzeros = {1: 1}`, `v2.nonzeros = {4: 2}`
- Iterate v1: 1 not in v2 → skip.
- Total: **0** ✓ (no overlap)

### Example 3: `nums1 = [0,1,0,0,2,0,0]`, `nums2 = [1,0,0,0,3,0,4]` → 6

- `v1.nonzeros = {1: 1, 4: 2}`, `v2.nonzeros = {0: 1, 4: 3, 6: 4}`
- Only index 4 in common: 2 × 3 = **6** ✓

### Two-pointer trace on Example 3

| step | i | j | idx_a | idx_b | action |
|------|---|---|-------|-------|--------|
| 1 | 0 | 0 | 1 | 0 | a > b → j++ |
| 2 | 0 | 1 | 1 | 4 | a < b → i++ |
| 3 | 1 | 1 | 4 | 4 | match! result += 6 |
| 4 | 2 | 2 | — | — | i out → exit |

Result: **6** ✓

### Edge cases
- Both all zeros: empty dicts → 0
- One all zeros: empty intersection → 0
- Both fully dense: hash map still works, just no savings
- Identical vectors: sum of squares of nonzero values

---

## LAYER 3: Key Insights

| Approach | __init__ | dotProduct | Space | Notes |
|----------|----------|-----------|-------|-------|
| Hash map (editorial) | O(n) | O(min(K_a, K_b)) | O(K) | **Best for sparse** |
| Hash + smaller-side | O(n) | O(min(K_a, K_b)) | O(K) | Slightly faster |
| Two-pointer on tuples | O(n) | O(K_a + K_b) | O(K) | No hashing |
| Dense (nums as-is) | O(n) | O(n) | O(n) | Wastes on zeros |

K = number of nonzeros.

**Why Medium:**

Tests **data structure choice**. Dense version `sum(zip)` is O(n) and trivially correct. The Medium insight: recognize that storing all zeros wastes memory, design a representation that matches the data structure.

This is **sparse vector / sparse matrix** territory — foundational for numerical computing, ML (sparse feature vectors), graph theory (sparse adjacency matrices).

**The follow-up answered:**

> "What if only one of the vectors is sparse?"

If v1 is sparse but v2 is dense (regular array):

```python
class SparseVector:
    def __init__(self, nums):
        self.nonzeros = {i: n for i, n in enumerate(nums) if n != 0}
    
    def dotProduct(self, dense_vec: List[int]) -> int:
        # dense_vec is a list. For each nonzero index in self,
        # access dense_vec[i] in O(1) (array indexing)
        return sum(val * dense_vec[i] for i, val in self.nonzeros.items())
```

Optimal: O(K) where K = nonzeros. Dense vector supports O(1) indexed access without hashing.

General principle: sparse vs dense isn't just storage — it dictates which access pattern is fast. Match the algorithm to the data structure.

**Why class design (not just function):**

`SparseVector` encapsulates:
- Storage (`__init__` decides representation)
- Operations (`dotProduct` is public API)
- Future extensions (could add `norm()`, `__add__`, etc.)

OOP fundamentals: a class is **data structure + operations**. Real production code is full of this.

**Sparse representations in real systems:**

- **CSR (Compressed Sparse Row)** — three arrays for sparse matrices
- **COO (Coordinate)** — list of (row, col, value)
- **DOK (Dictionary of Keys)** — `{(row, col): value}` (this problem's hash map = 1D version)
- **scipy.sparse** provides all of these

**Time complexity nuance:**

For "K_a in v1, K_b in v2":
- Hash map: iterate smaller → O(K_a)
- Two-pointer: walk both → O(K_a + K_b)

Hash wins for lopsided sizes. Two-pointer wins on similar sizes (cache-friendly). Mention both in interviews.

---

## LAYER 4: Interview Variations

• **Sparse matrix multiplication (LC 311):** 2D generalization.
• **Sparse ADD/SUBTRACT:** Result has nonzeros where EITHER input does. Merge dicts.
• **Norm:** `sum(v**2 for v in self.nonzeros.values()) ** 0.5`.
• **Cosine similarity:** `dot(v1, v2) / (norm(v1) * norm(v2))`.
• **k-th largest in sparse vector:** Heap on `nonzeros.values()`.
• **Vector with HUGE indices (10¹⁸):** Hash map mandatory.
• **One sparse, one dense:** See "follow-up" in Layer 3.
• **Both dense:** Skip abstraction; use plain arrays.
• **Streaming dot product:** Maintain running sum on new entries.
• **Negative values:** Same algorithm works.
• **Near-zero threshold:** Replace `if n != 0` with `if abs(n) > epsilon`.
• **Persistent (immutable):** Frozen sets of (i, val) tuples.
• **Many queries vs same vector:** Pre-compute once, query many times. Class-based design supports this.
• **L1 norm:** `sum(abs(v) for v in self.nonzeros.values())`.

---

## LAYER 5: Cheat Sheet

```python
# Preferred — hash map with dict comp
class SparseVector:
    def __init__(self, nums: List[int]):
        self.nonzeros = {i: n for i, n in enumerate(nums) if n != 0}
    
    def dotProduct(self, vec: 'SparseVector') -> int:
        result = 0
        for i, n in self.nonzeros.items():
            if i in vec.nonzeros:
                result += n * vec.nonzeros[i]
        return result
```

**Editorial form:**
```python
class SparseVector:
    def __init__(self, nums):
        self.nonzeros = {}
        for i, n in enumerate(nums):
            if n != 0:
                self.nonzeros[i] = n
    
    def dotProduct(self, vec):
        result = 0
        for i, n in self.nonzeros.items():
            if i in vec.nonzeros:
                result += n * vec.nonzeros[i]
        return result
```

**Optimized — iterate smaller:**
```python
class SparseVector:
    def __init__(self, nums):
        self.nonzeros = {i: n for i, n in enumerate(nums) if n != 0}
    
    def dotProduct(self, vec):
        a, b = self.nonzeros, vec.nonzeros
        if len(a) > len(b):
            a, b = b, a
        return sum(val * b[i] for i, val in a.items() if i in b)
```

**One-liner with set intersection:**
```python
class SparseVector:
    def __init__(self, nums):
        self.nonzeros = {i: n for i, n in enumerate(nums) if n != 0}
    
    def dotProduct(self, vec):
        common = self.nonzeros.keys() & vec.nonzeros.keys()
        return sum(self.nonzeros[i] * vec.nonzeros[i] for i in common)
```

**Two-pointer (no hashing):**
```python
class SparseVector:
    def __init__(self, nums):
        self.nonzeros = [(i, n) for i, n in enumerate(nums) if n != 0]
    
    def dotProduct(self, vec):
        i, j, result = 0, 0, 0
        while i < len(self.nonzeros) and j < len(vec.nonzeros):
            idx_a, val_a = self.nonzeros[i]
            idx_b, val_b = vec.nonzeros[j]
            if idx_a == idx_b:
                result += val_a * val_b
                i += 1
                j += 1
            elif idx_a < idx_b:
                i += 1
            else:
                j += 1
        return result
```

**Follow-up — one sparse, one dense:**
```python
class SparseVector:
    def __init__(self, nums):
        self.nonzeros = {i: n for i, n in enumerate(nums) if n != 0}
    
    def dotProductDense(self, dense: List[int]) -> int:
        return sum(val * dense[i] for i, val in self.nonzeros.items())
```

---

**Time:** O(n) for `__init__`, O(min(K_a, K_b)) for `dotProduct` | **Space:** O(K)

**Pattern flag:** "Lots of zeros that don't contribute" → **sparse representation**. Replace O(n) dense storage with hash map `{index: value}` keyed by nonzero indices. Dot product (or sum, or product) iterates only nonzeros, skipping trivial 0 contributions. Foundational idea behind sparse matrices, ML feature vectors, graph adjacency. When you see "most entries are zero," reach for this pattern first.
