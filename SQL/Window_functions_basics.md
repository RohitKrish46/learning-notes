# Window Function Basics

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


## Window Function Rules

1. Window functions can only appear in the `SELECT` or `ORDER BY` clauses.
2. **Nesting** of window functions is **not allowed**.
3. Window functions are executed **after the `WHERE` clause** (i.e., on filtered data).
4. You can use `GROUP BY` and window functions in the same query **only if** they use the same grouping columns.


## Summary

* Use **GROUP BY** when you need to summarize data into groups.
* Use **WINDOW FUNCTIONS** when you want to aggregate but still see row-level details.
* The power of window functions lies in combining `PARTITION BY`, `ORDER BY`, and `FRAME` clauses to create dynamic, rolling, or grouped calculations.

