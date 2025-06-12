# Data Manipulation Language (DML)

DML commands are used to manage data within tables in a database. The key DML operations are `INSERT`, `UPDATE`, and `DELETE`.

---

## INSERT

Used to add new rows to a table.

### Insert Using Manual Entry

```sql
INSERT INTO table_name (col1, col2, col3)
VALUES (val1, val2, val3);
````

* If column names are not specified, values must be provided for **all columns**, in the table's defined order.
* You must match the number and order of columns with the values.
* You can insert multiple rows in a single query by providing multiple sets of values:

```sql
INSERT INTO table_name (col1, col2)
VALUES 
  (val1, val2),
  (val3, val4),
  (val5, val6);
```

### Insert Using SELECT

Used to insert rows from another table using a query result:

```sql
INSERT INTO persons (id, person_name, birth_date, phone)
SELECT 
  id,
  first_name,
  NULL AS birth_date,
  'Unk' AS phone
FROM customers;
```

---

## 🔹 UPDATE

Used to modify existing rows in a table.

```sql
UPDATE table_name
SET col1 = val1,
    col2 = val2
WHERE condition;
```

> **Always include a `WHERE` clause unless you intend to update all rows.**

---

## 🔹 DELETE

Used to remove rows from a table.

```sql
DELETE FROM table_name
WHERE condition;
```

> **Always include a `WHERE` clause to avoid deleting all rows unintentionally.**

To remove **all rows** from a table:

```sql
TRUNCATE TABLE table_name;
```

* `TRUNCATE` is faster than `DELETE`, but cannot be rolled back in some systems.
* `TRUNCATE` resets identity columns (if any) and does not fire `ON DELETE` triggers.

---

###  Summary

| Command  | Purpose                | Use with WHERE? | Supports Rollback |
| -------- | ---------------------- | --------------- | ----------------- |
| INSERT   | Add new rows           | ❌ (optional)    | ✅                 |
| UPDATE   | Modify existing rows   | ✅ (recommended) | ✅                 |
| DELETE   | Remove specific rows   | ✅ (recommended) | ✅                 |
| TRUNCATE | Remove all rows (fast) | ❌               | ❌ *(in many DBs)* |


