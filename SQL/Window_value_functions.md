# Window Value Functions

**Window value functions** allow access to values from other rows while operating on the current row in a result set.

## Overview

- Common Functions: `LEAD`, `LAG`, `FIRST_VALUE`, `LAST_VALUE`
- An `ORDER BY` clause is **mandatory**.
- Works on **any data type**.
- `PARTITION BY` is **optional**.
- **Frame Clause Rules:**
  - `LEAD` and `LAG`: Cannot use frame clause.
  - `FIRST_VALUE`: Frame clause is optional.
  - `LAST_VALUE`: Frame clause **should be modified** for consistent output.


## LEAD & LAG

- **LEAD(column, offset, default)**  
  - Accesses a value from a future row (next row).
  - Example:
    ```sql
    LEAD(Sales, 2, 10) OVER (PARTITION BY ProductID ORDER BY OrderDate)
    ```
  - Without a default, the **last row** result is `NULL`.

- **LAG(column, offset, default)**  
  - Accesses a value from a past row (previous row).
  - Without a default, the **first row** result is `NULL`.

### Use Cases

1. **Time Series Analysis**  
   - Analyze data across time for patterns, trends, or changes.  
   - Example: Year-over-Year (YoY), Month-over-Month (MoM) comparisons.

2. **Customer Retention Analysis**  
   - Evaluate customer behavior across time periods to improve engagement and retention strategies.

## FIRST_VALUE & LAST_VALUE

- **FIRST_VALUE(column)**  
  - Returns the first value in the window.

- **LAST_VALUE(column)**  
  - Returns the last value in the window.
  - To get consistent results across all rows, the **frame clause must be modified**.

### Frame Clause for LAST_VALUE

- **Default**:  
  ```sql
  ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ```

* Limits LAST\_VALUE to return current row in some cases.

* **Recommended Change**:

  ```sql
  ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING
  ```

  * Ensures LAST\_VALUE returns the final row of the partition/window.

### Use Case

* **Compare to Extremes**

  * Evaluate how current values relate to the highest or lowest values within a partition/window.
  * Example: Compare monthly sales to the first and last month in the time window.


## Summary

* Window value functions enhance row-by-row comparisons in SQL without collapsing rows like aggregates.
* `LEAD` and `LAG` are used for **relative positioning**.
* `FIRST_VALUE` and `LAST_VALUE` are used for **extreme value comparisons**.
* Use `ORDER BY` and adjust frame clauses carefully for accurate results.
