
## Components of a SQL Query

A SQL query, also called a SQL statement, is used to retrieve and manipulate data from a database. Below is an example query and a breakdown of its components:

```sql
SELECT name, LOWER(color)
FROM customers
WHERE country = 'Italy'
```

### Clauses
A SQL query is divided into clauses, each serving a specific purpose:
- **SELECT Clause**: Specifies which columns to retrieve from the database.
- **FROM Clause**: Indicates the table from which data is queried.
- **WHERE Clause**: Filters rows based on a specified condition.

### Keywords
- Keywords are reserved words in SQL that define commands or operations (e.g., `SELECT`, `FROM`, `WHERE`).
- Best practice: Write keywords in uppercase for clarity.
- **Functions**: Special keywords like `LOWER` that take input, process it, and produce output. For example, `LOWER(color)` converts values in the `color` column to lowercase.
- Functions can transform data in various ways (e.g., `UPPER`, `SUM`, `AVG`).

### Identifiers
- Identifiers are names of database objects, such as:
  - **Columns**: e.g., `name`, `country`.
  - **Tables**: e.g., `customers`.
- They represent specific elements within the database.

### Operators
- Operators are used in the `WHERE` clause to compare values (e.g., `=` in `country = 'Italy'`).
- Common operators include `=`, `>`, `<`, `!=`, `LIKE`, etc.

## Key SQL Clauses and Their Roles

### SELECT & FROM
- **Purpose**: `SELECT` specifies the columns to retrieve, and `FROM` indicates the table.
- Example: `SELECT name, country, score FROM customers` retrieves specific columns.
- Use `SELECT *` to retrieve all columns from the table.
- **Execution**: SQL starts with the `FROM` clause to identify the table, then processes the `SELECT` clause to retrieve specified columns.

### WHERE
- **Purpose**: Filters rows based on a condition before any aggregation.
- Example: `WHERE score > 400` keeps only rows where the `score` column exceeds 400.
- **Execution Order**: `FROM` → `WHERE` → `SELECT`.

### ORDER BY
- **Purpose**: Sorts the result set in ascending (`ASC`) or descending (`DESC`) order.
- Example: `ORDER BY score DESC` sorts by the `score` column in descending order.
- Default: Ascending (`ASC`) if not specified.
- **Nested Sorting**: Sort by multiple columns (e.g., `ORDER BY country, score`) to handle duplicates. The first column is sorted, then the next.
- **Execution**: Applied after filtering and aggregation.

### GROUP BY
- **Purpose**: Groups rows with identical values in specified columns and applies aggregate functions (e.g., `SUM`, `AVG`).
- Example: `GROUP BY country` groups rows by unique `country` values, and `SUM(score)` calculates the total score for each country.
- **Rules**:
  - All columns in the `SELECT` clause must either be aggregated (e.g., `SUM(score)`) or included in the `GROUP BY` clause.
  - The result is determined by unique values in the grouped columns.
- **Execution**: Applied after `WHERE`.

### HAVING
- **Purpose**: Filters grouped (aggregated) data.
- Example: `HAVING SUM(score) > 800` filters groups where the total score exceeds 800.
- **Key Difference**:
  - Use `WHERE` to filter rows *before* aggregation.
  - Use `HAVING` to filter *after* aggregation (requires `GROUP BY`).
- **Execution**: Applied after `GROUP BY`.

### Example Query with Aggregation
```sql
SELECT country, SUM(score)
FROM customers
WHERE score > 400
GROUP BY country
HAVING SUM(score) > 800
```
- **Explanation**:
  - `FROM customers`: Selects the `customers` table.
  - `WHERE score > 400`: Filters rows where `score` exceeds 400.
  - `GROUP BY country`: Groups rows by unique `country` values.
  - `HAVING SUM(score) > 800`: Filters groups where the total score exceeds 800.
  - `SELECT country, SUM(score)`: Returns the `country` and total score for each group.
- **Execution Order**: `FROM` → `WHERE` → `GROUP BY` → `HAVING` → `SELECT`.

### DISTINCT
- **Purpose**: Removes duplicate rows from the result set, ensuring each value appears only once.
- Example: `SELECT DISTINCT country` returns unique `country` values.
- **Caution**: Avoid using `DISTINCT` unless necessary, as it can slow down query performance.
- **Execution**: Applied after `SELECT`.

### TOP
- **Purpose**: Limits the number of rows returned in the result set.
- Example: `SELECT TOP 5 name FROM customers` returns the first 5 rows.
- No conditions are applied; it simply restricts output to the top `n` rows.
- **Execution**: Applied last.

## Coding Order vs. Execution Order
SQL queries are written in a specific order, but the database executes them differently.

### Coding Order
- Write clauses in this sequence:
  - `SELECT`, `DISTINCT`, `TOP`
  - `FROM`
  - `WHERE`
  - `GROUP BY`
  - `HAVING`
  - `ORDER BY`
- **Filtering Types**:
  - `SELECT`: Filters columns.
  - `DISTINCT`: Removes duplicate rows.
  - `TOP`: Limits the number of rows.
  - `WHERE`: Filters rows before aggregation.
  - `HAVING`: Filters rows after aggregation.

### Execution Order
- The database processes the query in this sequence:
  - `FROM`: Identifies the table.
  - `WHERE`: Filters rows based on conditions.
  - `GROUP BY`: Groups rows for aggregation.
  - `HAVING`: Filters aggregated groups.
  - `SELECT`, `DISTINCT`: Retrieves specified columns and removes duplicates.
  - `ORDER BY`: Sorts the result set.
  - `TOP`: Limits the number of rows returned.

