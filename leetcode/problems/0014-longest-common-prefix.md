# 14. Longest Common Prefix

| | |
|---|---|
| **Difficulty** | Easy |
| **Family** | Strings |
| **Date** | 2026-07-21 |
| **Mode** | Reference Recall (ported from prior archive for comparison) |
| **Link** | https://leetcode.com/problems/longest-common-prefix/ |

---

## 1. Input / Output
```
input  → strs = ["flower","flow","flight"]
output → "fl"
```
Constraints that matter:
- `strs[i]` can be length 0 → empty-string case must return `""`.
- Up to 200 strings, each ≤ 200 chars → O(S) over total characters is plenty.

## 2. Data structure used
- Just a running `prefix` string. No auxiliary structure.

## 3. Why this approach works
**Horizontal scanning.** Assume the whole first string is the prefix, then walk the rest and
shrink the candidate from the right until it sits at the *front* of each string.
`strs[i].find(prefix) != 0` means "prefix isn't found starting at index 0," i.e. this string
doesn't begin with it → drop the last char and retry. Shrinks to `""` when nothing is common.

## 4. Time / space complexity
- Time: O(S), S = sum of all characters — worst case every char is compared.
- Space: O(1) beyond the output.

---

## My solution
```python
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
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

## What tripped me up
- The editorial variant I pasted had **`if len(strs) == 0`** with no colon → `SyntaxError`,
  the function never ran. Reminder: run it before calling it equivalent.
- Idioms to prefer next time (correct-but-not-Pythonic in mine):
  `not strs[i].startswith(prefix)` over `strs[i].find(prefix) != 0` (says what I mean,
  exits early); `prefix[:-1]` over `prefix[0:len(prefix)-1]`; `if not strs` over `len(strs)==0`.

---

## Next-day recall
| | |
|---|---|
| **Recall date** | — |
| **Rebuilt without looking?** | **Owed** (this was a port, not a cold rebuild) |
| **Where I got stuck** | — |

## Interview sentence
> Horizontal-scan prefix matching: start with the first string as the prefix and shrink it
> until every other string begins with it. I'd write the check as `startswith` rather than
> `find(...)==0` — same result, clearer intent, earlier exit.
