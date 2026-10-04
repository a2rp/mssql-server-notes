# 4. Read data with SELECT

[Back to notes index](../README.md)

| [Previous: Databases, schemas, tables, and data types](./03-databases-schemas-tables-and-types.md) | [Notes index](../README.md) | [Next: Filter rows with predicates](./05-filtering-and-predicates.md) |
| --- | --- | --- |

## Select the columns you need

`SELECT` reads rows and returns a result set. Name the columns the caller needs instead of returning every field:

~~~sql
SELECT LearnerId, DisplayName, Email
FROM learning.Learners;
GO
~~~

`SELECT *` is convenient while exploring a table, but it can return unnecessary data and makes a query depend on every current column. Explicit columns make the result easier to understand and safer when a table changes.

## Use aliases and expressions

An alias gives an output column a readable name. An expression can calculate a value without changing the stored row:

~~~sql
SELECT
    DisplayName AS LearnerName,
    Balance,
    Balance * 0.10 AS EstimatedTenPercent
FROM learning.Learners;
GO
~~~

Use `AS` to make aliases clear. Avoid spaces or punctuation in aliases when application code will consume the result. A calculated value is returned by the query; it is not stored unless an application writes it or the schema defines a computed column.

## Combine constants and functions

A query can return literal values or built-in function results along with table columns:

~~~sql
SELECT
    LearnerId,
    DisplayName,
    IsActive,
    SYSUTCDATETIME() AS ReadAtUtc
FROM learning.Learners;
GO
~~~

SQL Server functions can be affected by data type, collation, time zone, and configuration. Check the function's documented behavior when the value is used for a business rule.

## Remove duplicates deliberately

`DISTINCT` removes duplicate result rows across the selected columns:

~~~sql
SELECT DISTINCT IsActive
FROM learning.Learners;
GO
~~~

Use it when unique result values are part of the question. It can require sorting or hashing, so adding it to hide an unexpected join duplication may conceal a query problem.

## Read a stable result shape

A query's output is a table of named columns and values. Decide which columns belong in that output, give calculated values clear aliases, and keep application-facing result shapes stable. Filtering, sorting, and paging are handled in the next chapters.

## Check what you learned

1. What does a `SELECT` statement return?
2. Why is naming the needed columns preferable to `SELECT *` in application queries?
3. What does a column alias change?
4. Does an expression in a `SELECT` list update the stored row?
5. What is one use for returning a built-in function result?
6. What rows does `DISTINCT` remove?
7. Why might `DISTINCT` conceal a join problem?
8. What helps keep an application-facing result shape stable?

## References

- [SELECT statement](https://learn.microsoft.com/sql/t-sql/queries/select-transact-sql)
- [Select list](https://learn.microsoft.com/sql/t-sql/queries/select-clause-transact-sql)
- [Built-in functions](https://learn.microsoft.com/sql/t-sql/functions/functions)
