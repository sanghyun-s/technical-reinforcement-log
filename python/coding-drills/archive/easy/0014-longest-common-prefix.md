# 0014. Longest Common Prefix

**Difficulty:** Easy  
**Pattern:** String  
**Date Solved:** 2026-05-15  
**Acceptance Rate:** 47.5%  
**Status:** ✅

---

## Understanding the Goal

Find longest common prefix among all strings.

**Key insight:** Process character-by-character. Stop when mismatch found.

**Examples Visualized:**

```
strs = ["flower", "flow", "flight"]

Compare position 0: f, f, f ✓
Compare position 1: l, l, l ✓
Compare position 2: o, o, i ✗ STOP

Result: "fl"
```

---

## LAYER 1: Line-by-Line Explanation

```python
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        if not strs:
            return ""
        
        # Start with first string as prefix
        prefix = strs[0]
        
        # Compare prefix against each remaining string
        for i in range(1, len(strs)):
            # Keep shrinking prefix until it matches at start
            while strs[i].find(prefix) != 0:
                # Remove last character from prefix
                prefix = prefix[0:len(prefix) - 1]
                
                # If prefix empty, no common prefix exists
                if prefix == "":
                    return ""
        
        return prefix
```

---

## LAYER 2: Worked Examples

**Example 1: ["flower","flow","flight"]**
```
prefix = "flower"

Check "flow": find("flower") = -1 (not found)
  → prefix = "flowe"
  find("flowe") = -1
  → prefix = "flow"
  find("flow") = 0 ✓ continue

Check "flight": find("flow") = -1
  → prefix = "flo"
  find("flo") = -1
  → prefix = "fl"
  find("fl") = 0 ✓ continue

Result: "fl" ✓
```

**Example 2: ["dog","racecar","car"]**
```
prefix = "dog"

Check "racecar": find("dog") = -1
  → prefix = "do" → find = -1
  → prefix = "d" → find = -1
  → prefix = "" → return ""

Result: "" ✓
```

---

## LAYER 3: Key Insights

**Pattern:** Horizontal Scanning with String Shrinking

| Aspect | Value |
|--------|-------|
| **Time** | O(S) where S = sum of all characters |
| **Space** | O(1) |
| **Approach** | Compare prefix against each string |

**Common mistakes:**
- ❌ Comparing wrong strings (should compare to prefix)
- ❌ Using find() incorrectly (must equal 0, not just found)
- ❌ Not handling empty string case

**When to use:**
- Find common prefix in strings
- String pattern matching
- Trie alternative (but simpler)

---

## LAYER 4: Interview Variations

• **If question asks "vertical scanning instead"** → Iterate character positions instead of strings, compare column-by-column across all strings, stop at first mismatch

• **If question says "find common suffix"** → Reverse all strings, find common prefix, reverse result back

• **If question adds constraint "case-insensitive"** → Convert all to lowercase first, then find common prefix

• **If question asks "longest common substring (not prefix)"** → Different problem, use dynamic programming or sliding window

---

## LAYER 5: Cheat Sheet Entry

**Pattern:** String Prefix Matching

**Template:**
```python
def longestCommonPrefix(strs):
    if not strs:
        return ""
    prefix = strs[0]
    for i in range(1, len(strs)):
        while strs[i].find(prefix) != 0:
            prefix = prefix[0:len(prefix) - 1]
            if prefix == "":
                return ""
    return prefix
```

**Key insight:** `.find(prefix) == 0` checks if string starts with prefix

**Related problems:** #1408, #2401

---

**Complexity Analysis**
- **Time:** O(S) where S = sum of all characters
- **Space:** O(1)

**What I Learned**
- String find() method and its return value
- Efficient prefix checking
- Early termination when no common prefix
