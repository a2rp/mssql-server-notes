# 14. Transactions, locking, and isolation

[Back to notes index](../README.md)

| [Previous: Indexes and execution plans](./13-indexes-and-execution-plans.md) | [Notes index](../README.md) | [Next: Security, backup, and restore](./15-security-backup-and-restore.md) |
| --- | --- | --- |

## Keep related changes together

A transaction groups database statements into one unit. A committed transaction makes its changes permanent. A rolled-back transaction discards its changes. Transactions are commonly described by ACID: atomicity, consistency, isolation, and durability.

Use `TRY...CATCH` and `XACT_STATE()` to handle errors and roll back an active or uncommittable transaction:

~~~sql
SET XACT_ABORT ON;

BEGIN TRY
    BEGIN TRANSACTION;

    UPDATE learning.Learners
    SET Balance = Balance - 25.00
    WHERE LearnerId = 1;

    UPDATE learning.Learners
    SET Balance = Balance + 25.00
    WHERE LearnerId = 2;

    COMMIT TRANSACTION;
END TRY
BEGIN CATCH
    IF XACT_STATE() <> 0
        ROLLBACK TRANSACTION;

    THROW;
END CATCH;
GO
~~~

For a real transfer, check that both accounts exist and the source has sufficient funds inside the transaction. Keep transactions short so they hold locks for less time.

## Understand locks and blocking

SQL Server uses locks to coordinate concurrent work and protect data according to the isolation level. One transaction can block another when both need incompatible access to the same resource. A long-running transaction can hold locks and delay unrelated work.

A deadlock occurs when transactions wait on one another in a cycle. SQL Server chooses one transaction as a deadlock victim and rolls it back. Keep transactions short, access shared tables in a consistent order, and use indexes that reduce how many rows a statement must examine.

## Choose an isolation level deliberately

Isolation controls what a transaction can observe while other transactions run. SQL Server supports levels including `READ COMMITTED`, `REPEATABLE READ`, `SNAPSHOT`, and `SERIALIZABLE`. Database configuration can enable read-committed snapshot behavior, so check the actual database settings rather than assuming all environments behave alike.

Higher isolation can prevent more anomalies but may increase blocking or version-store use. Row-versioned reads can reduce reader and writer blocking, but they require appropriate configuration and storage monitoring. Select the weakest level that still meets the operation's correctness requirement.

## Check what you learned

1. What does a transaction group together?
2. Expand the ACID properties.
3. What is the purpose of `XACT_STATE()` in the error handler?
4. How does a long transaction affect other work?
5. What is a deadlock?
6. What does SQL Server do to resolve a deadlock cycle?
7. How can isolation level affect blocking and data visibility?
8. Why should database settings be checked instead of assuming a default behavior?

## References

- [Transactions and the Database Engine](https://learn.microsoft.com/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
- [SET TRANSACTION ISOLATION LEVEL](https://learn.microsoft.com/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
- [XACT_STATE](https://learn.microsoft.com/sql/t-sql/functions/xact-state-transact-sql)
- [Deadlocks guide](https://learn.microsoft.com/sql/relational-databases/sql-server-deadlocks-guide)
