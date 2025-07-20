# Indexes in Databases

## What is an Index?

- An index is a data structure that provides quick access to data, optimizing the speed of queries.
- Some indexes improve read performance, and some can improve write performance.

## Types of Indexes

### 1. Structured Indexes
- **Clustered Index**
- **Non-clustered Index**

### 2. Storage Indexes
- **Rowstore Index**
- **Columnstore Index**

### 3. Function Indexes
- **Unique Index**
- **Filtered Index**



## Why Do We Need Indexes? (Heap Structure)

- **Page** is the smallest unit of data storage in a DB (8KB). It stores rows, columns, metadata, etc.
- Two types of pages:
  - **Data Page**
  - **Index Page**

### Data Page Structure:
- **Header** (Fixed 96 bytes): Contains metadata like Page ID (FileID: Unique Page Number).
- **Data Rows Area**: Space where rows are stored.
- **Offset Array**: Index of each row within the page.

### Heap Structure:
- When data is stored in pages as inserted (unordered), this is called a **heap structure**.
- A **Heap Index** is a table without a clustered index.
- For data search, SQL performs a **full table scan** (row-by-row).
- **Fast writes, slow reads.**
- **Indexes are used to solve this inefficiency.**



## Structured Indexes

### Clustered Index

- Data is **sorted and stored** according to a column, forming a **B-Tree (Balanced Tree)**.
- In a clustered index:
  - **Leaf nodes = actual data pages**.
  - **Intermediate nodes = index nodes** with pointers to data pages.
- Improves read performance by reducing the number of pages scanned.

### Non-Clustered Index

- Data remains **unsorted**.
- A separate **index page** is created with mappings:

`Primary\_Key -> File ID : Page No : Row Offset`

- This mapping is known as a **Row Identifier (RID)**.
- The B-Tree's **leaf nodes = index entries**, not data.
- Requires additional lookup to fetch data from base table.



## Clustered vs Non-Clustered Index

| Feature | Clustered | Non-Clustered |
|--------|------------|----------------|
| Definition | Physically sorts and stores rows | Separate structure with pointers to data |
| Number of Indexes | One per table | Multiple allowed |
| Read Performance | Faster | Slower (extra lookup) |
| Write Performance | Slower (due to reordering) | Faster |
| Storage Efficiency | More efficient | Requires more storage |
| Use Cases | Unique columns, range queries, rarely modified | Joins, frequently filtered columns, exact matches |

- **Analogy**:
- Clustered Index: Table of Contents in a book.
- Non-Clustered Index: Glossary with word-to-page mappings.

---

## Syntax for Creating Indexes

```sql
-- Clustered or Non-Clustered Index
CREATE [CLUSTERED | NONCLUSTERED] INDEX index_name
ON table_name (col1, col2, col3 ...)
```

---

## Composite Indexes

```sql
-- Composite Index Creation
CREATE INDEX idx_DBCustomers_CountryScore
ON Sales.DBCustomers (Country, Score)

-- Index will be used
SELECT *
FROM Sales.DBCustomers
WHERE Country = 'Germany'

-- Index won't be used (Score is not the leftmost column)
SELECT *
FROM Sales.DBCustomers
WHERE Score < 600
```

### Notes:

* Use **composite indexes** for frequent filtering on multiple columns.
* **Left-Most Prefix Rule**: Index is used only if query filters start from the **first column** in the index and follow the defined order.

