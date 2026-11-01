> **Skill:** python / coding-drills (sliding window) · **Source:** LeetCode #3 · **Mode:** New · **Date:** 2026-11-01
> Difficulty: Medium · Family: Sliding Window / Hash

# 3. Longest Substring Without Repeating Characters

## The insight — the canonical sliding window
Brute force checks every substring for uniqueness: O(n³) (or O(n²) with early exit). The window trick: keep a window `[left, right]` that is **always duplicate-free**. Expand `right`; when the incoming char creates a duplicate, **shrink `left`** until it's unique again. Each char enters and leaves the window at most once → **O(n)**. Track the max width as you go.

## My solutions
```python
# A2 — sliding window with a Counter (the one to know), O(n)
from collections import Counter
def lengthOfLongestSubstring(self, s):
    chars = Counter(); left = res = 0
    for right in range(len(s)):
        chars[s[right]] += 1
        while chars[s[right]] > 1:        # duplicate just entered → shrink
            chars[s[left]] -= 1; left += 1
        res = max(res, right - left + 1)
    return res

# A1 — brute force: check(i, j) builds a set per substring. O(n^3)-ish. The baseline to beat.
```

## The pattern to carry
"Longest/shortest contiguous window satisfying a constraint" → **sliding window**: expand right, shrink left when the invariant breaks, record the extremum. Window width is `right - left + 1`. Same two-pointer family as the merge/partition drills.

## Complexity
A2: O(n) time (each index visited ≤ twice), O(min(n, alphabet)) space. A1: O(n³).

## Reproduce cold
The expand-right / shrink-left-while-duplicate loop with a counter; why it's O(n) not O(n²).

## Interview line candidate
> It's a sliding window: I keep a window that's always duplicate-free, expand the right edge each step, and when a repeat enters I shrink the left edge until it's gone — each character enters and leaves once, so it's O(n), versus O(n³) checking every substring.
