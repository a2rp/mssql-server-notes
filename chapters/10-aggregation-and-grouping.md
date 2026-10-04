# 10. Aggregate data with GROUP BY

[Back to notes index](../README.md)

| [Previous: Joins and combining result sets](./09-joins-and-set-operations.md) | [Notes index](../README.md) | [Next: Subqueries, CTEs, and window functions](./11-subqueries-ctes-and-window-functions.md) |
| --- | --- | --- |

## Summarize rows with aggregate functions

Aggregate functions calculate one value from a set of rows. Common functions include `COUNT`, `SUM`, `AVG`, `MIN`, and `MAX`:

~~~sql
SELECT
    COUNT(*) AS LearnerCount,
    AVG(Balance) AS AverageBalance,
    MIN(JoinedAt) AS FirstJoinTime,
    MAX(JoinedAt) AS LatestJoinTime
FROM learning.Learners;
GO
~~~

`COUNT(*)` counts rows. `COUNT(ColumnName)` counts rows where that expression is not `NULL`. Most other aggregate functions ignore `NULL` inputs. Check this behavior when missing values have a business meaning.

## Group rows before calculating

`GROUP BY` creates one result row per distinct group key and calculates aggregates for that group:

~~~sql
SELECT
    IsActive,
    COUNT(*) AS LearnerCount,
    SUM(Balance) AS TotalBalance
FROM learning.Learners
GROUP BY IsActive;
GO
~~~

Every selected column that is not aggregated must be part of the `GROUP BY` expression. A grouped result has fewer rows than its input when several source rows share each group key.

## Filter groups with HAVING

`WHERE` filters source rows before grouping. `HAVING` filters groups after aggregate values are calculated:

~~~sql
SELECT
    IsActive,
    COUNT(*) AS LearnerCount
FROM learning.Learners
GROUP BY IsActive
HAVING COUNT(*) >= 5;
GO
~~~

Use `WHERE` for a row-level condition and `HAVING` for an aggregate condition. Filtering earlier can reduce the rows that need grouping.

## Count distinct values

`COUNT(DISTINCT expression)` counts unique non-null values. Use it when the question is about distinct members rather than input rows:

~~~sql
SELECT COUNT(DISTINCT CourseId) AS CoursesWithEnrollments
FROM learning.Enrollments;
GO
~~~

Be careful when an aggregate follows joins. Joining one learner to several enrollment records repeats learner values in the joined result. Aggregate at the intended grain or use a distinct count when that matches the requirement.

## Check what you learned

1. What does an aggregate function calculate?
2. How do `COUNT(*)` and `COUNT(ColumnName)` differ?
3. What does `GROUP BY` determine?
4. Which selected columns must appear in `GROUP BY`?
5. How do `WHERE` and `HAVING` differ?
6. What does `COUNT(DISTINCT CourseId)` count?
7. How can a join affect an aggregate's input rows?
8. Why should the intended reporting grain be clear before grouping?

## References

- [Aggregate functions](https://learn.microsoft.com/sql/t-sql/functions/aggregate-functions-transact-sql)
- [GROUP BY](https://learn.microsoft.com/sql/t-sql/queries/select-group-by-transact-sql)
- [HAVING](https://learn.microsoft.com/sql/t-sql/queries/select-having-transact-sql)
- [COUNT](https://learn.microsoft.com/sql/t-sql/functions/count-transact-sql)
