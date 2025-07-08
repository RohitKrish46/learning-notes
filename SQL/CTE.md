# Common Table Expression (CTE)

CTE is a named result set (temporary table) that can be used multiple times within your query to simplify and organize complex queries.



## Difference Between Subquery and CTE

1. **Approach**:
   - **CTE**: Top-down approach — declare the CTE first, then write the main query.
   - **Subquery**: Bottom-up approach — subquery is written inside the main query.

2. **Reusability**:
   - **CTE**: Can be referenced multiple times in the main query.
   - **Subquery**: Can typically be used only once in a given context.

---

## Why Use CTE?

1. **Reduces Redundancy**: Allows repeated use of a computed result without rewriting logic.
2. **Improves Readability**: Modular structure makes complex queries easier to read and understand.
3. **Performance**: CTE results may be cached, offering faster repeated access.

> CTE is like a function in Python — reusable and modular.



## Types of CTEs

### 1. Non-Recursive CTE
- **Standalone CTE**: Independent and self-contained. The main query depends on the CTE.
  
  **Syntax**:
  ```sql
  WITH CTE_name AS (
      SELECT ...
      FROM ...
      WHERE ...
  )
  SELECT ...
  FROM CTE_name
  WHERE ...
  ```

> Note: `ORDER BY` clause is not allowed inside a CTE.

* **Multiple Standalone CTEs**: Multiple independent CTEs used in one query.

  **Syntax**:

  ```sql
  WITH CTE_name_1 AS (
      SELECT ...
      FROM ...
      WHERE ...
  ),
  CTE_name_2 AS (
      SELECT ...
      FROM ...
  )
  SELECT ...
  FROM CTE_name_1
  JOIN CTE_name_2 ON ...
  WHERE ...
  ```

* **Nested CTE**: One CTE depends on the result of another CTE.

  **Syntax**:

  ```sql
  WITH CTE_name_1 AS (
      SELECT ...
      FROM ...
      WHERE ...
  ),
  CTE_name_2 AS (
      SELECT ...
      FROM CTE_name_1
      WHERE ...
  )
  SELECT ...
  FROM CTE_name_2
  WHERE ...
  ```

  * Outer CTE is standalone
  * Inner CTE is dependent (non-standalone)



### 2. Recursive CTE

* A self-referencing CTE that repeats operations until a stop condition is met.
* Similar to a loop — it continues until a break condition.

**Syntax**:

```sql
WITH CTE_name AS (
    -- Anchor Query
    SELECT ...
    FROM ...
    WHERE ...

    UNION / UNION ALL

    -- Recursive Query
    SELECT ...
    FROM CTE_name
    WHERE ... -- Break condition
)
SELECT ...
FROM CTE_name
WHERE ...
```


## CTE Best Practices

* Rethink and refactor CTEs before introducing a new one.
* Avoid using more than 5 CTEs in a single query to maintain readability.
* Do not use ORDER BY inside the CTE definition.