# Subqueries in SQL

A **subquery** is a query nested inside another SQL query.

## Types of Subqueries

### 1. Dependency-Based Types

- **Non-Correlated Subquery**  
  - Can run independently of the main query.
  - Executed once, result reused by the main query.
  - Better performance, easier to understand.

- **Correlated Subquery**  
  - Depends on values from the main query.
  - Executed once per row of the main query.
  - Slower performance, more complex.
  - Example: Uses main query’s column inside the subquery.

| Category       | Non-Correlated Subquery                 | Correlated Subquery                          |
|----------------|------------------------------------------|-----------------------------------------------|
| Definition     | Independent of the main query            | Dependent on the main query                   |
| Execution Time | Executed once                           | Executed for each row                         |
| Standalone     | Can be run separately                   | Cannot be run separately                      |
| Readability    | Easier                                  | More complex                                  |
| Performance    | Better                                   | Poor (row-by-row evaluation)                  |
| Use Case       | Static filters, constant comparisons     | Dynamic row-level filtering                   |



### 2. Result-Based Types

- **Scalar Subquery**  
  Returns a single value.  
  Example: `(SELECT AVG(Sales) FROM SalesData)`

- **Row Subquery**  
  Returns multiple rows of a single column.

- **Table Subquery**  
  Returns multiple rows and columns.


### 3. Location-Based Types

#### a. Subquery in `FROM`

- Acts as a temporary table.
- Can be used for intermediate filtering or transformations.

```sql
SELECT *
FROM (SELECT col1, col2 FROM Table WHERE Condition) AS SubTable
```

#### b. Subquery in `SELECT`

* Adds scalar values (side-by-side) to main query results.
* Must return a **single value** (scalar).

```sql
SELECT
    Col1,
    (SELECT MAX(Sales) FROM SalesData WHERE Region = 'West') AS MaxSales
FROM Employees
```

#### c. Subquery in `JOIN`

* Prepares data before joining.
* Can involve filtering or aggregation before merge.

```sql
SELECT e.EmployeeID, s.TotalSales
FROM Employees e
JOIN (
    SELECT EmployeeID, SUM(Sales) AS TotalSales
    FROM Sales
    GROUP BY EmployeeID
) s ON e.EmployeeID = s.EmployeeID
```

#### d. Subquery in `WHERE`

* Adds filtering logic to the main query.
* **Scalar subqueries** can be used with comparison operators.
* **Row/table subqueries** can be used with logical operators.

**Operators Used:**

1. **Comparison Operators**: `=`, `<`, `>`, etc.
   (Expect scalar subquery result)

2. **Logical Operators**: (Expect multiple values)

   * `IN` – Checks if a value exists in the subquery result.
   * `NOT IN` – Checks if a value does not exist in the result.
   * `ANY` – Returns true if **any** value matches the condition.

     ```sql
     WHERE Salary > ANY (SELECT Salary FROM Employees)
     ```
   * `ALL` – Returns true if **all** values match the condition.

     ```sql
     WHERE Salary > ALL (SELECT Salary FROM Employees)
     ```
   * `EXISTS` – Checks if the subquery returns **any rows**.

     * We only care whether rows exist, not their values.

     ```sql
     SELECT *
     FROM Employees e
     WHERE EXISTS (
         SELECT 1 FROM Sales s WHERE s.EmployeeID = e.EmployeeID
     )
     ```



## Summary

* Subqueries are versatile tools used within other queries for filtering, calculating, and transforming data.
* **Scalar**, **Row**, and **Table** subqueries differ in the number of values they return.
* **Non-Correlated** subqueries are more performant, while **Correlated** subqueries enable dynamic, row-wise comparisons.
* Subqueries can be used in `SELECT`, `FROM`, `JOIN`, and `WHERE` clauses, each serving different purposes.

