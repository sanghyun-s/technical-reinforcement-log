# 3368. First Letter Capitalization

| | |
|---|---|
| **Difficulty** | Hard |
| **Family** | SQL (recursive CTE / numbers table) |
| **Date** | 2026-08-15 |
| **Mode** | Reference Recall (A1 hand-typed/submitted, A2 researched) |
| **Link** | https://leetcode.com/problems/first-letter-capitalization/ |

---

## 1. Problem
Title-case every word in `content_text`: first letter upper, rest lower, spaces preserved.

## 2. The real difficulty
SQL has **no built-in split-string-into-rows**. To title-case each word you first need
one-word-per-row, and there's no `for` loop — so you must *manufacture* iteration. The
capitalization itself is trivial: `UPPER(first) + LOWER(rest)`.

## 3. Two ways to fake a loop in SQL
- **Recursive CTE — "peel one word per iteration":** base case grabs the first word
  (`REGEXP_SUBSTR('^[a-zA-Z]+')`) + keeps the remainder; recursive case repeats on the shrinking
  remainder, `rn+1`, terminating at `remain = ''`. SQL mirror of DSA recursion: base case + a
  step that **shrinks the input** + termination.
- **Numbers table + `SUBSTRING_INDEX` — "index into positions":** build `seq` 1..100,
  `CROSS JOIN` every row with every n, extract the n-th word. `WHERE` caps n at the word count.

## 4. Idioms worth memorizing
- **Count words by length difference:**
  `CHAR_LENGTH(t) - CHAR_LENGTH(REPLACE(t,' ','')) + 1` = (# spaces) + 1 = word count. The
  standard "count occurrences of a char" trick.
- **Nth delimited field:** `SUBSTRING_INDEX(SUBSTRING_INDEX(t, ' ', n), ' ', -1)` — inner =
  "first n words," outer `-1` = "last of those" = the n-th word.

---

## My solutions (both correct)
```sql
-- Approach 1 — recursive CTE (peel), then title-case, then GROUP_CONCAT back
WITH RECURSIVE cte AS (
  SELECT content_id, content_text,
         TRIM(REGEXP_SUBSTR(content_text,'^[a-zA-Z]+')) AS word,
         TRIM(REGEXP_REPLACE(content_text,'^[a-zA-Z]+','')) AS remain, 1 AS rn
  FROM user_content
  UNION ALL
  SELECT content_id, content_text,
         TRIM(REGEXP_SUBSTR(remain,'^[a-zA-Z]+')),
         TRIM(REGEXP_REPLACE(remain,'^[a-zA-Z]+','')), rn+1
  FROM cte WHERE remain <> ''
)
SELECT content_id, content_text AS original_text,
       GROUP_CONCAT(CONCAT(UPPER(SUBSTR(word,1,1)), LOWER(SUBSTR(word,2)))
                    ORDER BY rn SEPARATOR ' ') AS converted_text
FROM cte GROUP BY content_id ORDER BY content_id;

-- Approach 2 — numbers table + SUBSTRING_INDEX (index the n-th word)
```

## Notes / caveats
- **Peel (recursive CTE) vs index (numbers table):** both turn one row into N. Numbers-table is
  usually preferred in interviews (recursive CTEs have depth limits, harder to reason about);
  recursive reads more procedural. Know both + when to pick each.
- Both rely on the problem's guarantee of **single spaces, no punctuation** — real text with
  double spaces/punctuation would break both (empty words, regex gaps). Worth stating.

## Interview sentence
> SQL has no split-to-rows, so I manufacture it — a recursive CTE that peels one word per
> iteration, or a numbers table cross-joined and indexed with `SUBSTRING_INDEX` for the n-th
> word. I'd use the numbers-table version to avoid recursion-depth limits; then title-casing is
> `UPPER(first) + LOWER(rest)` per word, re-joined with `GROUP_CONCAT`.
