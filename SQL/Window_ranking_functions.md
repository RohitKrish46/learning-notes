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


## Summary

* Use `ROW_NUMBER`, `RANK`, `DENSE_RANK`, and `NTILE` for discrete ranking tasks.
* Use `CUME_DIST` and `PERCENT_RANK` for distribution-based analysis.
* Always use `ORDER BY` with ranking functions.
* Avoid `FRAME` clauses in ranking queries.
