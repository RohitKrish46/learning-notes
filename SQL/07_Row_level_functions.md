Here's your content converted into clean, organized notes in `.md` (Markdown) format for easy future reading and reference.

---

````markdown
# Row-Level Functions in SQL

## What is a Function in SQL?

A function in SQL is built-in code that accepts an input value, processes it, and returns an output value.  

**Types of SQL Functions:**

1. **Single-Row Functions**  
   - One row in → one row out  
   - Example: String, Numeric, Date & Time, Null functions  

2. **Multi-Row Functions (Aggregations)**  
   - Multiple rows in → one value out  
   - Example: `SUM()`  
     - Aggregate Functions (Basic)  
     - Window Functions (Advanced)

**Nested Functions Example:**  
`Input → Function1() → Output → Function2() → Final Output`

---

## String Functions

**Categories:**
1. **Manipulation**: `CONCAT`, `UPPER`, `LOWER`, `TRIM`, `REPLACE`
2. **Calculation**: `LEN`
3. **Extraction**: `LEFT`, `RIGHT`, `SUBSTRING`

### Common String Functions

- **CONCAT(col1, '-', col2)**  
  Combines multiple values into one.

- **UPPER(col)** / **LOWER(col)**  
  Converts to upper/lower case.

- **TRIM(col)**  
  Removes leading/trailing spaces.

- **REPLACE(val, old, new)**  
  Replaces characters.  
  Example: `REPLACE('123-', '-', '')`

- **LEN(val)**  
  Returns the number of characters.  
  Example: `LEN('and') → 3`

- **LEFT(value, n)** / **RIGHT(value, n)**  
  Extracts characters from start or end.

- **SUBSTRING(value, start, length)**  
  Extracts part of a string from a given position.

---

## Number Functions

- **ROUND(value, decimals)**  
  Rounds a number.  
  Example: `ROUND(3.516, 2) → 3.52`

- **ABS(value)**  
  Returns absolute value (positive).

---

## Date and Time Functions

### Categories

- **Part Extraction**: `DAY`, `MONTH`, `YEAR`, `DATEPART`, `DATENAME`, `DATETRUNC`, `EOMONTH`
- **Format and Casting**: `FORMAT`, `CONVERT`, `CAST`
- **Calculations**: `DATEADD`, `DATEDIFF`
- **Validation**: `ISDATE`

> `GETDATE()` gives the current date.

### Part Extraction

- **DAY(date)** / **MONTH(date)** / **YEAR(date)**
- **DATEPART(part, date)**  
  Returns numeric part (e.g., week, quarter).
- **DATENAME(part, date)**  
  Returns string (e.g., "August", "Sunday").
- **DATETRUNC(part, date)**  
  Truncates to part (e.g., month start).
- **EOMONTH(date)**  
  Returns end of the month.

### Format and Casting

- International formats: `YYYY-MM-DD`, `MM-DD-YYYY`, etc.
- **FORMAT(value, format [, culture])**
- **CONVERT(datatype, value)**
- **CAST(value AS datatype)**

### Calculations

- **DATEADD(part, interval, date)**  
  Adds interval to date.
- **DATEDIFF(part, start, end)**  
  Difference between dates.

### Validation

- **ISDATE(value)**  
  Checks for valid date.

---

## Null Functions

NULL represents a missing or unknown value.

```sql
NULL ≠ 0  
NULL ≠ ''  
NULL ≠ ' '
````

### Replacing Nulls

* **ISNULL(value, replacement)**
* **COALESCE(val1, val2, ..., valn)**
  Returns the first non-null value.

**ISNULL vs COALESCE:**

| Feature       | ISNULL          | COALESCE        |
| ------------- | --------------- | --------------- |
| Value Count   | 2 values only   | Multiple values |
| Speed         | Faster          | Slower          |
| Compatibility | SQL Server only | All databases   |

**Use Cases:**

* Handle nulls before:

  * Aggregations (`SUM`, `AVG`, etc.)
  * Math operations
  * Joins
  * Sorting (nulls sort lowest)

### NULLIF

* **NULLIF(val1, val2)**
  Returns NULL if both values are equal, else returns val1.
  *Use case:* Prevent divide-by-zero.

### IS NULL / IS NOT NULL

* **Syntax**:
  `col IS NULL`
  `col IS NOT NULL`

**Use Cases:**

* Searching for missing values
* Anti-joins (unmatched rows)

### NULL vs Empty vs Blank

|             | NULL           | Empty String (`''`) | Blank Space (`' '`) |
| ----------- | -------------- | ------------------- | ------------------- |
| Meaning     | Unknown        | Known but empty     | Known, with space   |
| Type        | Special Marker | String (0)          | String (≥1)         |
| Storage     | Minimal        | Low                 | Highest             |
| Comparison  | `IS NULL`      | `= ''`              | `= ' '`             |
| Performance | Best           | Fast                | Slow                |

### Null Handling Policies

1. Use only NULLs and empty strings

   * Use `TRIM()` to remove spaces
2. Use only NULLs (avoid blanks & empties)

   * Use `NULLIF()` to convert `''` to `NULL`
3. Use default values (e.g., `'unknown'`)

   * Use `COALESCE()` to fill NULLs

**Recommended Usage:**

* **Policy 2**: During data prep and DB inserts (for performance)
* **Policy 3**: While displaying reports (for readability)

### Summary

* NULL represents missing data.
* Use `COALESCE` to replace NULLs.
* Use `NULLIF` to replace values with NULL.
* Check for NULLs with `IS NULL` and `IS NOT NULL`.

---

## CASE Statement

Used to evaluate conditions and return results like an `if-else`.

### Syntax

```sql
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ...
    ELSE result -- Optional
END
```

### Quick Form

```sql
CASE column
    WHEN val1 THEN result1
    WHEN val2 THEN result2
    ...
    ELSE result
END
```

**Notes:**

* If `ELSE` is omitted, NULL is returned by default.
* SQL stops at the first matched condition.
* All result types must match.

### Use Cases

1. **Data Transformation**

   * Categorize or bucket data
2. **Value Mapping**

   * Convert codes to labels
3. **Handling NULLs**

   * Replace NULLs with defaults
4. **Conditional Aggregation**

   * Aggregate based on specific conditions

---

