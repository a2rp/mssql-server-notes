# 13. Indexes and execution plans

[Back to notes index](../README.md)

| [Previous: Views and stored procedures](./12-views-and-stored-procedures.md) | [Notes index](../README.md) | [Next: Transactions, locking, and isolation](./14-transactions-locking-and-isolation.md) |
| --- | --- | --- |

## How an index helps a query

An index is an ordered structure that can help SQL Server find rows or return them in a useful order without reading every row in a table. A clustered rowstore index determines the order of the table's data pages by its key. A nonclustered index stores key values and row locators separately from the table data.

Indexes consume storage and must be maintained when data changes. A query can use an index seek to navigate to matching keys or an index scan to read many index entries. A scan is not automatically bad; it can be appropriate when a query needs a large part of the data.

## Create an index for a query pattern

If the application often filters learners by active state and returns their names, an index can include the output column:

~~~sql
CREATE NONCLUSTERED INDEX IX_Learners_IsActive
ON learning.Learners (IsActive)
INCLUDE (DisplayName, JoinedAt);
GO
~~~

Key columns help locate or order rows. Included columns can cover a query's projection without becoming part of the index key order. This increases index size, so include only columns that support measured queries.

A unique index enforces uniqueness. A filtered index stores only rows that meet a predicate and can suit a stable subset of the workload:

~~~sql
CREATE NONCLUSTERED INDEX IX_Learners_Active
ON learning.Learners (JoinedAt)
INCLUDE (DisplayName)
WHERE IsActive = 1;
GO
~~~

## Read an execution plan

Use SSMS to include an actual execution plan, then run a representative query. The plan shows operators selected for that execution and estimated versus actual row counts:

~~~sql
SET STATISTICS IO ON;
SET STATISTICS TIME ON;
GO

SELECT LearnerId, DisplayName, JoinedAt
FROM learning.Learners
WHERE IsActive = 1
ORDER BY JoinedAt DESC;
GO

SET STATISTICS IO OFF;
SET STATISTICS TIME OFF;
GO
~~~

Compare logical reads, elapsed time, rows estimated, and rows actually returned. A seek is not a goal by itself. Check whether the complete plan does reasonable work for the size and shape of the result.

## Keep predicates index-friendly

Applying a function to a filtered column can make it harder for SQL Server to seek into a conventional index:

~~~sql
-- Often harder to seek on a JoinedAt index
WHERE YEAR(JoinedAt) = 2026

-- Express the same year as a range
WHERE JoinedAt >= '2026-01-01'
  AND JoinedAt <  '2027-01-01'
~~~

Prefer a range on the stored value when it is equivalent. Parameter types should also match the column type to avoid implicit conversions that change the plan.

## Check what you learned

1. What work can an index help SQL Server avoid?
2. How do clustered and nonclustered indexes differ?
3. What does an included column contribute to a nonclustered index?
4. What constraint can a unique index enforce?
5. Why is an index scan not automatically a problem?
6. Which measurements can help compare query work?
7. Why can `YEAR(JoinedAt) = 2026` make index use harder?
8. Why should indexes be added based on measured queries?

## References

- [SQL Server index architecture](https://learn.microsoft.com/sql/relational-databases/sql-server-index-design-guide)
- [Create indexes](https://learn.microsoft.com/sql/relational-databases/indexes/create-nonclustered-indexes)
- [Execution plans](https://learn.microsoft.com/sql/relational-databases/performance/execution-plans)
- [SET STATISTICS IO](https://learn.microsoft.com/sql/t-sql/statements/set-statistics-io-transact-sql)
