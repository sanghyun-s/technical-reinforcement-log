> **Skill:** python / coding-drills (linked list, elementary math) · **Source:** LeetCode #2 · **Mode:** New (not from archive) · **Date:** 2026-11-01
> Difficulty: Medium · Family: Linked List

# 2. Add Two Numbers

## Problem
Two non-empty linked lists hold a number per node, **digits in reverse order**. Add them, return the sum as a linked list. `[2,4,3] + [5,6,4] = [7,0,8]` (342+465=807).

## The insight
Reverse-order storage is the gift: the list head is the *ones* digit, so you add left-to-right exactly like grade-school column addition — digit + digit + **carry**, write `sum % 10`, carry `sum // 10`. The loop condition is the whole trick: continue **while either list has nodes OR a carry remains** (so `99999 + 9999` grows a new leading node).

## My solutions
```python
# A1 — iterative with a dummy head (the idiomatic submit)
def addTwoNumbers(self, l1, l2):
    dummy = ListNode(0); curr = dummy; carry = 0
    while l1 or l2 or carry:                 # the key 3-part condition
        s = (l1.val if l1 else 0) + (l2.val if l2 else 0) + carry
        carry = s // 10
        curr.next = ListNode(s % 10); curr = curr.next
        l1 = l1.next if l1 else None
        l2 = l2.next if l2 else None
    return dummy.next

# A2 — recursive, carry passed forward as a parameter; base case = no nodes AND no carry
```
**Dummy-head pattern:** build off a throwaway node so you never special-case "the first real node"; return `dummy.next`. Reusable for any list-building problem.

## Complexity
O(max(m,n)) time, O(max(m,n)) for the result list. A1 is O(1) extra; A2 adds O(n) call-stack.

## Reproduce cold
The `while l1 or l2 or carry` loop with dummy head and `divmod`-style carry; state why reverse order makes head-first addition correct.

## Interview line candidate
> Digits are stored reversed, so the head is the ones place — I add column by column with a carry, looping while either list has nodes *or* a carry is left, which handles the extra leading digit. A dummy head lets me build the result without special-casing the first node.
