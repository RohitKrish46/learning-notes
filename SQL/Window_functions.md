# Window Functions

Window functions perform calculations across a defined range of rows (a "window") without collapsing the rows themselves.

## Group By vs Window Functions

| Feature                  | GROUP BY                              | WINDOW FUNCTION                                |
|--------------------------|----------------------------------------|------------------------------------------------|
| Output Granularity       | One row per group                      | One row per original record                    |
| Use Case                 | Simple aggregation                     | Aggregation + row-level detail                 |
| Functions Supported      | Only aggregate functions               | Aggregate, Rank, Value functions               |
| Flexibility              | Less flexible                          | More powerful for complex analyses             |
| Filtering Support        | Yes (WHERE and HAVING)                 | No direct filtering; apply filters before      |


## Why Use Window Functions?

- **Limitation of GROUP BY**:
  - Cannot return both aggregated and individual-level details at the same time.
  - All columns in SELECT must appear in GROUP BY.
- **Window functions solve this** by allowing aggregations while preserving row-level detail.


## Syntax

```sql
[Function(column)] OVER (
    PARTITION BY ...
    ORDER BY ...
    ROWS BETWEEN ...
)
```

* **Function** (`f(x)`): Aggregate, Rank, or Value function
* **OVER Clause**: Indicates that the function is used in a windowed context


## PARTITION BY Clause

* Divides the dataset into groups (windows) based on specified columns.
* If not used, the entire dataset is treated as a single window.

## ORDER BY Clause

* Sorts data within each partition.
* **Required** for Rank and Value functions.
* **Optional** for Aggregate functions.
* When used, SQL **automatically applies a FRAME clause**:

  ```sql
  ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
  ```

## FRAME Clause

Defines the exact set of rows within the window to include for calculations.

**Syntax**:

```sql
ROWS BETWEEN [lower bound] AND [upper bound]
```

* **Frame Types**: `ROWS`, `RANGE`
* **Lower Bound Options**:

  * `CURRENT ROW`
  * `N PRECEDING`
  * `UNBOUNDED PRECEDING`
* **Upper Bound Options**:

  * `CURRENT ROW`
  * `N FOLLOWING`
  * `UNBOUNDED FOLLOWING`

**Notes**:

* Frame clause is **only valid with ORDER BY**.
* Enables **sliding window** and rolling aggregations.

## Types of Window Functions

1. [Window Aggregate Fucntions](#window-aggregate-functions)
2. [Window Ranking Functions](#window-ranking-functions)
3. [Window Value Functions](#window-value-functions)

## Window Function Rules

1. Window functions can only appear in the `SELECT` or `ORDER BY` clauses.
2. **Nesting** of window functions is **not allowed**.
3. Window functions are executed **after the `WHERE` clause** (i.e., on filtered data).
4. You can use `GROUP BY` and window functions in the same query **only if** they use the same grouping columns.

---

# Window Aggregate Functions

Window aggregate functions perform cumulative or grouped analysis without collapsing rows, and they can work across a defined window using `OVER()`.



## Syntax

```sql
[AGGREGATE_FUNCTION(column)] OVER (
    PARTITION BY ...  -- optional
    ORDER BY ...      -- optional
)
```

* `f(x)` must be a numeric column (except for `COUNT`)
* All functions **ignore NULLs** by default, unless handled explicitly



## COUNT

* Counts rows in a column or table
* `COUNT(*)` counts all rows including NULLs
* `COUNT(column)` counts only non-NULL rows
* Works on all data types

### Use Cases

1. **Overall Analysis**: Total rows over entire window
2. **Category Analysis**: Count rows within each partition
3. **Data Quality Checks**:

   * Identify NULL values
   * Find duplicates



## SUM

* Calculates the sum of values within a window
* Accepts only numeric columns
* Ignores NULLs

### Use Cases

1. **Overall Analysis** across all rows
2. **Category-wise Totals**
3. **Comparison Analysis**

   * Compare current value to total window sum



## AVG

* Returns average of numeric values in a window
* Ignores NULLs
* To include NULLs, use `COALESCE(column, 0)` or similar pre-handling

### Use Cases

1. **Overall Averages**
2. **Category Averages**
3. **Comparison Analysis**

   * Compare row value to average of window



## MIN and MAX

* `MIN()` returns the smallest value in a window
* `MAX()` returns the largest value in a window
* Ignores NULLs unless handled explicitly

### Use Cases

1. **Overall Minimum/Maximum**
2. **Category Extremes**
3. **Comparison Analysis**

   * Compare row values to min/max of partition or entire set



## Running and Rolling Totals

Running and rolling totals are used to observe cumulative or windowed trends over time.

### Running Total

* Accumulates values **from the beginning** to the current row
* All past rows are included
* Default frame clause:

  ```sql
  ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
  ```

**Example**:

```sql
SUM(Sales) OVER (ORDER BY Month)
```

### Rolling Total

* Accumulates values over a **fixed window** (e.g., 30 days or 3 rows)
* As new rows enter, old ones exit the frame
* Useful for moving averages or short-term trends

**Example**:

```sql
SUM(Sales) OVER (
  ORDER BY Month 
  ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
)
```

### Use Cases

1. **Tracking Progress**

   * e.g., Cumulative Sales vs. Targets
2. **Trend Analysis**

   * Understand recent vs. past performance patterns

---

# Window Ranking Functions

Window ranking functions are used to rank rows within a partitioned result set.


## Ranking Types

1. **Integer-Based Ranking**
   - Assigns discrete ranks starting from 1.
   - Used for Top/Bottom N analysis (e.g., Top 3 products).
   - Functions: `ROW_NUMBER`, `RANK`, `DENSE_RANK`, `NTILE`

2. **Percentage-Based Ranking**
   - Assigns ranks in percentages from 0 to 1.
   - Values are evenly distributed.
   - Used in Distribution analysis (e.g., Top 20% products).
   - Functions: `CUME_DIST`, `PERCENT_RANK`


## General Syntax

```sql
RANK() OVER (
    PARTITION BY column -- optional
    ORDER BY column     -- required
)
```

* `ORDER BY` clause is **mandatory**
* `FRAME` clause is **not allowed**
* `NTILE()` requires a number as input; others do not accept inputs


## Integer-Based Ranking Functions

### ROW\_NUMBER()

* Assigns a **unique number** to each row
* **Does not handle ties**
* No gaps in ranks

### RANK()

* Assigns the same rank for tied values
* **Handles ties**
* Gaps appear after ties

### DENSE\_RANK()

* Assigns the same rank for tied values
* **Handles ties**
* **No gaps** in ranks

#### Comparison Table

| Function     | Tie Handling | Gaps in Rank | Unique Values |
| ------------ | ------------ | ------------ | ------------- |
| `ROW_NUMBER` | No           | No           | Yes           |
| `RANK`       | Yes          | Yes          | No            |
| `DENSE_RANK` | Yes          | No           | No            |

### Use Cases

1. **Top/Bottom N Analysis**

   * Identify top performers for marketing or rewards
2. **Underperformance Analysis**

   * Identify lagging segments or regions
3. **Assigning Unique IDs**

   * Use as primary keys, or for pagination
4. **Deduplication**

   * Identify and remove duplicates


## NTILE(n)

* Divides result set into `n` equal buckets
* Formula:

  ```text
  Bucket Size = (Total Rows) / (Number of Buckets)
  ```
* Buckets at the beginning may have 1 more row when division has a remainder

### Use Cases

1. **Data Segmentation** (Data Analysts)

   * Create quantile-based groups (quartiles, deciles, etc.)
2. **Load Balancing** (Data Engineers)

   * Break up large tables into equal parts for distribution/migration


## Percentage-Based Ranking Functions

### CUME\_DIST()

* Calculates cumulative distribution:

  ```text
  CUME_DIST = (Position of current row) / (Total number of rows)
  ```
* **Inclusive**: includes current row in distribution
* Tied values share the **last occurrence position**

### PERCENT\_RANK()

* Calculates relative percentage rank:

  ```text
  PERCENT_RANK = (Position of current row - 1) / (Total rows - 1)
  ```
* **Exclusive**: does not include the current row
* Tied values share the **first occurrence position**

---
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

* Use **GROUP BY** when you need to summarize data into groups.
* Use **WINDOW FUNCTIONS** when you want to aggregate but still see row-level details.
* The power of window functions lies in combining `PARTITION BY`, `ORDER BY`, and `FRAME` clauses to create dynamic, rolling, or grouped calculations.

* Aggregate functions within windows provide powerful insight without grouping rows.
* Use `PARTITION BY` to group analysis, `ORDER BY` to define progression.
* Use `COALESCE` to handle NULLs when needed.
* Running totals accumulate over time; rolling totals analyze over a fixed window.

* Use `ROW_NUMBER`, `RANK`, `DENSE_RANK`, and `NTILE` for discrete ranking tasks.
* Use `CUME_DIST` and `PERCENT_RANK` for distribution-based analysis.
* Always use `ORDER BY` with ranking functions.
* Avoid `FRAME` clauses in ranking queries.

* Window value functions enhance row-by-row comparisons in SQL without collapsing rows like aggregates.
* `LEAD` and `LAG` are used for **relative positioning**.
* `FIRST_VALUE` and `LAST_VALUE` are used for **extreme value comparisons**.
* Use `ORDER BY` and adjust frame clauses carefully for accurate results.