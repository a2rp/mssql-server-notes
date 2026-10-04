# 11. Subqueries, CTEs, and window functions

[Back to notes index](../README.md)

| [Previous: Aggregate data with GROUP BY](./10-aggregation-and-grouping.md) | [Notes index](../README.md) | [Next: Views and stored procedures](./12-views-and-stored-procedures.md) |
| --- | --- | --- |

## Use a subquery for a related question

A subquery is a query nested inside another statement. A scalar subquery can provide one value to an outer query:

~~~sql
SELECT LearnerId, DisplayName, Balance
FROM learning.Learners
WHERE Balance > (
    SELECT AVG(Balance)
    FROM learning.Learners
);
GO
~~~

The inner query calculates an average. The outer query returns learners whose balance is greater than that value. A scalar subquery must return one value when used in this position.

## Use EXISTS to check related rows

`EXISTS` tests whether a subquery returns at least one row. It can express a relationship check without returning the matching child rows:

~~~sql
SELECT l.LearnerId, l.DisplayName
FROM learning.Learners AS l
WHERE EXISTS (
    SELECT 1
    FROM learning.Enrollments AS e
    WHERE e.LearnerId = l.LearnerId
);
GO
~~~

`NOT EXISTS` can find learners without enrollments. It is often a clear choice for existence checks and avoids confusion about nullable values that can occur with some `NOT IN` queries.

## Name a query with a CTE

A common table expression (CTE) names a result used by the statement that follows it. It can make a multi-step query easier to read:

~~~sql
WITH ActiveLearners AS (
    SELECT LearnerId, DisplayName, JoinedAt
    FROM learning.Learners
    WHERE IsActive = 1
)
SELECT LearnerId, DisplayName, JoinedAt
FROM ActiveLearners
WHERE JoinedAt >= '2026-01-01';
GO
~~~

A CTE is scoped to one statement. It does not automatically store or cache its result. Use a temporary table when an intermediate result needs indexes or reuse across several statements.

## Add calculations without collapsing rows

An aggregate with `GROUP BY` returns one row per group. A window function calculates across related rows while keeping each source row:

~~~sql
SELECT
    LearnerId,
    DisplayName,
    IsActive,
    JoinedAt,
    ROW_NUMBER() OVER (
        PARTITION BY IsActive
        ORDER BY JoinedAt DESC, LearnerId ASC
    ) AS PositionWithinStatus
FROM learning.Learners;
GO
~~~

`PARTITION BY` divides rows into groups for the calculation. The `ORDER BY` inside `OVER` defines the calculation's order. A unique tie-breaker makes row numbering predictable when dates repeat.

Use functions such as `ROW_NUMBER`, `RANK`, `SUM() OVER`, and `LAG` when each output row needs context from neighboring or grouped rows. Window ordering does not itself guarantee the final result order; add an outer `ORDER BY` when display order matters.

## Check what you learned

1. What is a subquery?
2. What requirement applies to a scalar subquery in a comparison?
3. What question does `EXISTS` answer?
4. How can `NOT EXISTS` find rows with no related records?
5. How long is a CTE available?
6. Does a CTE automatically cache its result?
7. How does a window function differ from a grouped aggregate?
8. Why may a window function need an outer `ORDER BY` as well?

## References

- [Subqueries](https://learn.microsoft.com/sql/relational-databases/performance/subqueries)
- [Common table expressions](https://learn.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql)
- [OVER clause](https://learn.microsoft.com/sql/t-sql/queries/select-over-clause-transact-sql)
- [Ranking functions](https://learn.microsoft.com/sql/t-sql/functions/ranking-functions-transact-sql)
