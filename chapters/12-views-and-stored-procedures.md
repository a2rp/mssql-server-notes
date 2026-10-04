# 12. Views and stored procedures

[Back to notes index](../README.md)

| [Previous: Subqueries, CTEs, and window functions](./11-subqueries-ctes-and-window-functions.md) | [Notes index](../README.md) | [Next: Indexes and execution plans](./13-indexes-and-execution-plans.md) |
| --- | --- | --- |

## Create a view for a reusable result

A view is a named query. A regular view stores its definition, not a separate copy of the result rows:

~~~sql
CREATE OR ALTER VIEW learning.ActiveLearners
AS
    SELECT LearnerId, DisplayName, Email, JoinedAt
    FROM learning.Learners
    WHERE IsActive = 1;
GO

SELECT LearnerId, DisplayName
FROM learning.ActiveLearners;
GO
~~~

Views can give users a stable, limited result shape and hide query complexity. Underlying schema changes can affect a view, so check dependencies when changing tables. An indexed view has extra requirements and storage behavior; do not assume every view is materialized.

## Create a stored procedure with parameters

A stored procedure is a named T-SQL routine that can accept parameters and run statements. Define parameter types explicitly:

~~~sql
CREATE OR ALTER PROCEDURE learning.FindLearners
    @IsActive BIT = NULL
AS
BEGIN
    SET NOCOUNT ON;

    SELECT LearnerId, DisplayName, IsActive
    FROM learning.Learners
    WHERE @IsActive IS NULL OR IsActive = @IsActive;
END;
GO

EXEC learning.FindLearners @IsActive = 1;
GO
~~~

Procedures can group database work, apply permissions, and provide a stable call interface. Keep their behavior documented and handle errors with an explicit policy. Parameter values should remain parameters rather than being concatenated into dynamic SQL text.

## Know where functions fit

Built-in functions calculate values inside a statement. User-defined functions can encapsulate reusable expressions or return a table, but have rules and performance considerations that differ from procedures. A function is best when the logic naturally behaves like a value or table expression. A procedure is suitable for a sequence of commands or a data operation with an explicit call.

Do not create a view, procedure, or function only to hide a query that is already clear. Give the database object a focused responsibility and grant access through roles or object permissions where that fits the application.

## Check what you learned

1. What does a regular view store?
2. Give one reason to expose a view instead of a base table.
3. What can happen when a view's underlying table changes?
4. Why should stored procedure parameters have explicit types?
5. How can a stored procedure be called with a named argument?
6. Why should values not be concatenated into dynamic SQL?
7. When might a function be a better fit than a procedure?
8. What should be clear about the responsibility of a database object?

## References

- [CREATE VIEW](https://learn.microsoft.com/sql/t-sql/statements/create-view-transact-sql)
- [CREATE PROCEDURE](https://learn.microsoft.com/sql/t-sql/statements/create-procedure-transact-sql)
- [User-defined functions](https://learn.microsoft.com/sql/relational-databases/user-defined-functions/user-defined-functions)
- [Parameterize queries](https://learn.microsoft.com/sql/relational-databases/security/sql-injection)
