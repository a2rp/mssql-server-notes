# 7. Insert, update, and delete data

[Back to notes index](../README.md)

| [Previous: Sort, page, and shape results](./06-sorting-pagination-and-projection.md) | [Notes index](../README.md) | [Next: Keys, constraints, and relationships](./08-keys-constraints-and-relationships.md) |
| --- | --- | --- |

## Insert rows and read generated values

Name the target columns so the statement stays clear if the table later gains another column. `OUTPUT inserted` can return the generated key:

~~~sql
INSERT INTO learning.Learners (DisplayName, Email)
OUTPUT inserted.LearnerId, inserted.DisplayName
VALUES (N'Mina Rao', N'mina@example.com');
GO
~~~

The insert must provide every required column that lacks a default. A unique constraint can reject a duplicate email, and a check constraint can reject a value outside its rule.

## Update only the intended rows

Use a selective `WHERE` clause. Preview the same condition with `SELECT` before changing data:

~~~sql
DECLARE @LearnerId INT = 1;

SELECT LearnerId, DisplayName, IsActive
FROM learning.Learners
WHERE LearnerId = @LearnerId;

UPDATE learning.Learners
SET IsActive = 0
WHERE LearnerId = @LearnerId;
GO
~~~

An `UPDATE` without a `WHERE` clause changes every row. Check the affected-row count and confirm that it matches the expected result.

## Delete selected rows carefully

Preview the target set first. In a practice database, wrap the delete in a transaction and roll it back while learning:

~~~sql
BEGIN TRANSACTION;

SELECT LearnerId, DisplayName
FROM learning.Learners
WHERE LearnerId = 1;

DELETE FROM learning.Learners
WHERE LearnerId = 1;

SELECT @@ROWCOUNT AS DeletedRows;
ROLLBACK TRANSACTION;
GO
~~~

`ROLLBACK` undoes the delete in this example. In an application operation that should persist, commit only after the application has confirmed its checks. A `DELETE` without a `WHERE` clause removes every row from the table.

## Use parameters in application queries

Applications should send values as parameters instead of joining user input into SQL text. A parameter keeps a value separate from the statement and helps prevent SQL injection. The JavaScript chapter shows the `mssql` package pattern.

For a set-based change, use one statement when possible. If the change spans related tables or must be all-or-nothing, use a transaction with error handling, covered later.

## Check what you learned

1. Why name insert columns explicitly?
2. How can an insert return a generated identity value?
3. What can prevent an insert from storing a duplicate email?
4. What is the risk of an `UPDATE` without a `WHERE` clause?
5. What should be checked before deleting rows?
6. What does `ROLLBACK` do in the practice example?
7. Why are application values sent as parameters?
8. When might several data changes need a transaction?

## References

- [INSERT statement](https://learn.microsoft.com/sql/t-sql/statements/insert-transact-sql)
- [UPDATE statement](https://learn.microsoft.com/sql/t-sql/queries/update-transact-sql)
- [DELETE statement](https://learn.microsoft.com/sql/t-sql/statements/delete-transact-sql)
- [OUTPUT clause](https://learn.microsoft.com/sql/t-sql/queries/output-clause-transact-sql)
