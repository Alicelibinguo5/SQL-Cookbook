# SQL Pattern Memorization Cheat Sheet

## Easy Problem Patterns (< 3 minutes each)

### Pattern 1: Simple Aggregation
```sql
SELECT col, COUNT(*), SUM(val) 
FROM table 
GROUP BY col 
ORDER BY COUNT(*) DESC;
```

### Pattern 2: Basic JOIN
```sql
SELECT t1.col, t2.col 
FROM table1 t1 
JOIN table2 t2 ON t1.id = t2.id;
```

### Pattern 3: Date Filtering
```sql
SELECT * 
FROM table 
WHERE date_col >= '2023-01-01' 
  AND date_col < '2024-01-01';
```

### Pattern 4: String Operations
```sql
SELECT UPPER(name), LENGTH(email) 
FROM users 
WHERE email LIKE '%@gmail.com';
```

### Pattern 5: NULL Handling
```sql
SELECT * 
FROM table 
WHERE col IS NOT NULL 
  AND col != 'exclude_value';
```

---

## Medium Problem Patterns (< 5 minutes each)

### Pattern 1: Window Functions with Ranking
```sql
SELECT employee_id, salary, 
       DENSE_RANK() OVER (PARTITION BY dept ORDER BY salary DESC) as rank
FROM employees;
```

### Pattern 2: Self-Join for Comparisons
```sql
SELECT DISTINCT p1.id 
FROM table p1 
JOIN table p2 ON p1.id = p2.id 
               AND p1.date < p2.date 
               AND p1.status != p2.status;
```

### Pattern 3: Subquery Filtering
```sql
SELECT * 
FROM table1 
WHERE id IN (
    SELECT id 
    FROM table2 
    WHERE condition
);
```

### Pattern 4: CTE with Aggregation
```sql
WITH summary AS (
    SELECT group_col, COUNT(*) cnt 
    FROM table 
    GROUP BY group_col
)
SELECT t.*, s.cnt 
FROM table t 
JOIN summary s ON t.group_col = s.group_col;
```

### Pattern 5: Date Calculations
```sql
SELECT *, 
       DATEDIFF(day, start_date, end_date) as duration 
FROM events;
```
---

## Hard Problem Patterns (< 7 minutes each)

### Pattern 1: Complex Window Functions
```sql
SELECT id, val,
       val - LAG(val, 1, 0) OVER (PARTITION BY group_id ORDER BY date_col) as diff,
       SUM(val) OVER (PARTITION BY group_id ORDER BY date_col 
                      ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) as cumsum
FROM table;
```

### Pattern 2: Recursive CTE
```sql
WITH RECURSIVE hierarchy AS (
    -- Base case
    SELECT id, parent_id, name, 1 as level 
    FROM table 
    WHERE parent_id IS NULL
    
    UNION ALL
    
    -- Recursive case
    SELECT t.id, t.parent_id, t.name, h.level + 1
    FROM table t 
    JOIN hierarchy h ON t.parent_id = h.id
)
SELECT * FROM hierarchy;
```

### Pattern 3: Multiple CTEs with Complex Logic
```sql
WITH base AS (
    SELECT ... FROM ... WHERE ...
),
filtered AS (
    SELECT ... FROM base WHERE ...
),
aggregated AS (
    SELECT ... FROM filtered GROUP BY ...
)
SELECT * FROM aggregated WHERE condition;
```

### Pattern 4: Advanced Pivoting
```sql
SELECT id,
    SUM(CASE WHEN month_col = 1 THEN revenue END) as jan,
    SUM(CASE WHEN month_col = 2 THEN revenue END) as feb,
    SUM(CASE WHEN month_col = 3 THEN revenue END) as mar,
    SUM(CASE WHEN month_col = 4 THEN revenue END) as apr,
    SUM(CASE WHEN month_col = 5 THEN revenue END) as may,
    SUM(CASE WHEN month_col = 6 THEN revenue END) as jun
FROM sales 
GROUP BY id;
```

### Pattern 5: Median/Percentile Calculations
```sql
-- For median
SELECT PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) as median_salary
FROM employees;

-- For quartiles
SELECT 
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY salary) as q1,
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) as median,
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY salary) as q3
FROM employees;
```

---

## Common Template Patterns

### Top N per Group
```sql
SELECT * 
FROM (
    SELECT *, 
           ROW_NUMBER() OVER (PARTITION BY group_col ORDER BY metric DESC) rn
    FROM table_name
) 
WHERE rn <= N;
```

### Running Calculations
```sql
SELECT col, 
       SUM(val) OVER (ORDER BY date_col) as running_sum,
       AVG(val) OVER (ORDER BY date_col ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) as moving_avg
FROM table;
```

### Lag/Lead Comparisons
```sql
SELECT *, 
       val - LAG(val) OVER (ORDER BY date_col) as diff,
       LEAD(val) OVER (ORDER BY date_col) as next_val
FROM table;
```

### Pivot Aggregation
```sql
SELECT id,
    SUM(CASE WHEN type = 'A' THEN amount END) as type_a,
    SUM(CASE WHEN type = 'B' THEN amount END) as type_b,
    SUM(CASE WHEN type = 'C' THEN amount END) as type_c
FROM table 
GROUP BY id;
```

---

## Keyword → Pattern Mapping

| Keywords | Pattern | Template |
|----------|---------|----------|
| "Nth highest/lowest" | Ranking | `DENSE_RANK() OVER (ORDER BY col DESC/ASC)` |
| "consecutive", "streak" | Gap Analysis | `LAG/LEAD comparison` |
| "pivot", "cross-tab" | Pivoting | `CASE WHEN with GROUP BY` |
| "median" | Percentile | `PERCENTILE_CONT(0.5)` |
| "running", "cumulative" | Window Sum | `SUM() OVER (ORDER BY)` |
| "previous/next" | Lag/Lead | `LAG/LEAD` |
| "rank within group" | Partition Ranking | `ROW_NUMBER() OVER (PARTITION BY)` |
| "hierarchy", "tree" | Recursive | `WITH RECURSIVE` |
| "session", "trip" | Gap & Islands | `LAG comparison + SUM OVER` |
| "retention", "cohort" | Time Series | `Multiple CTEs with date math` |

---

## Speed Hacks

### Common Abbreviations
- `s` = SELECT
- `f` = FROM  
- `w` = WHERE
- `g` = GROUP BY
- `o` = ORDER BY

### Pre-memorized Aliases
- `t1, t2, t3` for table aliases
- `rn` for ROW_NUMBER()
- `rk` for RANK()
- `cnt` for COUNT()

### Instant Snippets
```sql
-- Ranking template
ROW_NUMBER() OVER (PARTITION BY {} ORDER BY {} DESC) rn

-- Lag template  
LAG({}) OVER (ORDER BY {})

-- Running sum template
SUM({}) OVER (ORDER BY {})

-- CTE template
WITH cte AS (
    SELECT ... FROM ... WHERE ...
)
SELECT ... FROM cte;
```

---

## Problem Classification (Instant Recognition)

### Easy (2-3 min)
- Basic SELECT with WHERE
- Simple JOINs
- GROUP BY aggregations
- Date/string functions
- NULL handling

### Medium (4-5 min)
- Window functions (ranking, lag/lead)
- Subqueries (EXISTS, IN, correlated)
- Self-joins
- CTEs
- Date calculations

### Hard (6-7 min)
- Complex window functions
- Recursive CTEs
- Advanced analytics (median, percentiles)
- Multiple table joins with complex conditions
- Gap and islands problems
- Session/cohort analysis

---

