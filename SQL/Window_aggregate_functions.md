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



## Summary

* Aggregate functions within windows provide powerful insight without grouping rows.
* Use `PARTITION BY` to group analysis, `ORDER BY` to define progression.
* Use `COALESCE` to handle NULLs when needed.
* Running totals accumulate over time; rolling totals analyze over a fixed window.
