# 3368. First Letter Capitalization

**Difficulty:** Hard  
**Pattern:** SQL / Recursive CTE / String Manipulation  
**Date Solved:** 2026-05-16  
**Status:** ✅

---

## Understanding the Goal

Convert text to title case: first letter of each word uppercase, all others lowercase, preserve spaces.

**Key insight:** Split text into individual words using recursion, capitalize each, then rejoin.

---

## LAYER 1: SQL Solution Explained

```sql
WITH RECURSIVE cte AS (
    -- BASE CASE: Extract first word
    SELECT 
        content_id,
        content_text,
        -- Extract first word (letters only from start)
        TRIM(REGEXP_SUBSTR(content_text, '^[a-zA-Z]+')) AS word,
        -- Remove first word, keep remainder
        TRIM(REGEXP_REPLACE(content_text, '^[a-zA-Z]+', '')) AS remain,
        1 AS rn  -- Row number to track word position
    FROM user_content 
    
    UNION ALL
    
    -- RECURSIVE CASE: Extract remaining words
    SELECT 
        content_id,
        content_text,
        -- Extract next word from remainder
        TRIM(REGEXP_SUBSTR(remain, '^[a-zA-Z]+')) AS word,
        -- Remove that word from remainder
        TRIM(REGEXP_REPLACE(remain, '^[a-zA-Z]+', '')) AS remain,
        rn + 1  -- Increment position
    FROM cte
    WHERE remain <> ''  -- Stop when no more words
),

-- Transform each word: uppercase first letter, lowercase rest
cta AS (
    SELECT 
        content_id,
        content_text,
        -- Capitalize: UPPER(first letter) + LOWER(rest of word)
        CONCAT(
            UPPER(SUBSTR(word, 1, 1)),
            LOWER(SUBSTR(word, 2))
        ) AS word,
        rn
    FROM cte        
)

-- Final: Join all capitalized words back together
SELECT 
    content_id,
    content_text AS original_text,
    -- GROUP_CONCAT: joins words in order with space separator
    GROUP_CONCAT(word ORDER BY rn SEPARATOR ' ') AS converted_text
FROM cta
GROUP BY content_id
ORDER BY content_id;
```

---

## LAYER 2: Worked Example

**Input: "hello world of SQL"**

**RECURSIVE CTE Execution:**

```
Iteration 1 (rn=1):
  word = REGEXP_SUBSTR("hello world of SQL", '^[a-zA-Z]+') = "hello"
  remain = REGEXP_REPLACE("hello world of SQL", '^[a-zA-Z]+', '') = " world of SQL"
  remain = TRIM(" world of SQL") = "world of SQL"

Iteration 2 (rn=2):
  word = REGEXP_SUBSTR("world of SQL", '^[a-zA-Z]+') = "world"
  remain = TRIM("of SQL") = "of SQL"

Iteration 3 (rn=3):
  word = REGEXP_SUBSTR("of SQL", '^[a-zA-Z]+') = "of"
  remain = TRIM("SQL") = "SQL"

Iteration 4 (rn=4):
  word = REGEXP_SUBSTR("SQL", '^[a-zA-Z]+') = "SQL"
  remain = ""
  STOP (remain = '')
```

**CTA (Capitalization):**

```
rn=1: word="hello" → CONCAT(UPPER(h), LOWER(ello)) = "Hello"
rn=2: word="world" → CONCAT(UPPER(w), LOWER(orld)) = "World"
rn=3: word="of" → CONCAT(UPPER(o), LOWER(f)) = "Of"
rn=4: word="SQL" → CONCAT(UPPER(S), LOWER(QL)) = "Sql"
```

**GROUP_CONCAT (Join):**

```
GROUP_CONCAT(word ORDER BY rn SEPARATOR ' ')
= "Hello World Of Sql"
```

---

## LAYER 3: Key Insights

| Aspect | Detail |
|--------|--------|
| **Time** | O(n × m) where n=rows, m=words per row |
| **Space** | O(n × m) for CTE storage |
| **Recursion** | Processes one word per iteration |
| **REGEXP_SUBSTR** | Extracts first word matching pattern |
| **REGEXP_REPLACE** | Removes matched pattern |
| **TRIM** | Removes leading/trailing spaces |
| **GROUP_CONCAT** | Reassembles words with separator |

**Why recursion?**
- Can't predict number of words
- Needs to loop until remainder is empty
- Tracks word position with `rn` for ordering

---

## LAYER 4: Interview Variations

• **If asked "preserve spaces between words"** → Modify separator logic  
• **If asked "handle punctuation"** → Adjust REGEXP pattern  
• **If PostgreSQL** → Use `STRING_AGG()` instead of `GROUP_CONCAT()`  

---

## LAYER 5: Cheat Sheet

```sql
WITH RECURSIVE word_split AS (
    SELECT 
        id,
        original_text,
        TRIM(REGEXP_SUBSTR(original_text, '^[a-zA-Z]+')) AS word,
        TRIM(REGEXP_REPLACE(original_text, '^[a-zA-Z]+', '')) AS remain,
        1 AS position
    FROM table_name
    
    UNION ALL
    
    SELECT 
        id,
        original_text,
        TRIM(REGEXP_SUBSTR(remain, '^[a-zA-Z]+')) AS word,
        TRIM(REGEXP_REPLACE(remain, '^[a-zA-Z]+', '')) AS remain,
        position + 1
    FROM word_split
    WHERE remain <> ''
),
capitalized AS (
    SELECT 
        id,
        original_text,
        CONCAT(UPPER(SUBSTR(word, 1, 1)), LOWER(SUBSTR(word, 2))) AS word,
        position
    FROM word_split
)
SELECT 
    id,
    original_text,
    GROUP_CONCAT(word ORDER BY position SEPARATOR ' ') AS converted_text
FROM capitalized
GROUP BY id;
```

---

**Time:** O(n × m) | **Space:** O(n × m)

---

## Key Learnings

✅ Recursive CTE for iterating until condition  
✅ REGEXP_SUBSTR to extract patterns  
✅ REGEXP_REPLACE to remove patterns  
✅ GROUP_CONCAT to reassemble with separator  
✅ ORDER BY in GROUP_CONCAT to maintain sequence
