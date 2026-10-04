# 9. Joins and combining result sets

[Back to notes index](../README.md)

| [Previous: Keys, constraints, and relationships](./08-keys-constraints-and-relationships.md) | [Notes index](../README.md) | [Next: Aggregate data with GROUP BY](./10-aggregation-and-grouping.md) |
| --- | --- | --- |

## Match related rows with INNER JOIN

An `INNER JOIN` returns rows where the join condition matches on both sides. Use explicit `JOIN` syntax and qualify columns with their table aliases:

~~~sql
SELECT
    l.LearnerId,
    l.DisplayName,
    c.CourseCode,
    c.Title
FROM learning.Learners AS l
INNER JOIN learning.Enrollments AS e
    ON e.LearnerId = l.LearnerId
INNER JOIN learning.Courses AS c
    ON c.CourseId = e.CourseId;
GO
~~~

The join condition expresses how related rows connect. If a learner has three enrollments, that learner appears in three result rows. This is expected row multiplication from a one-to-many relationship.

## Keep unmatched rows with LEFT JOIN

A `LEFT JOIN` keeps every row from its left input. Columns from the right side are `NULL` when there is no match:

~~~sql
SELECT l.LearnerId, l.DisplayName, e.CourseId
FROM learning.Learners AS l
LEFT JOIN learning.Enrollments AS e
    ON e.LearnerId = l.LearnerId;
GO
~~~

To find learners without enrollments, check a right-side non-nullable key for `IS NULL`. If a right-side filter belongs in the join condition, placing it in `WHERE` can remove unmatched rows and change the outer join's behavior.

~~~sql
SELECT l.LearnerId, l.DisplayName
FROM learning.Learners AS l
LEFT JOIN learning.Enrollments AS e
    ON e.LearnerId = l.LearnerId
WHERE e.LearnerId IS NULL;
GO
~~~

## Other ways to combine results

- `RIGHT JOIN` keeps all rows from its right input. Queries are often easier to follow when rewritten with the kept table on the left.
- `FULL OUTER JOIN` keeps matched and unmatched rows from both inputs.
- `CROSS JOIN` returns every combination of rows from both inputs. Use it only when every combination is intended.
- `UNION` combines compatible result shapes and removes duplicate rows.
- `UNION ALL` combines compatible result shapes and keeps duplicates.

The two sides of a `UNION` must return the same number of columns in compatible types and corresponding order. Output column names come from the first query:

~~~sql
SELECT DisplayName AS PersonName FROM learning.Learners
UNION ALL
SELECT Title AS PersonName FROM learning.Courses;
GO
~~~

## Check join cardinality

Before adding another join, know whether each side can match one row or many. A missing or incomplete join predicate can multiply rows and distort totals. Compare row counts and inspect a small, known record set before relying on the result.

## Check what you learned

1. Which rows does an `INNER JOIN` return?
2. Which input's rows are preserved by a `LEFT JOIN`?
3. Why might one learner appear several times in a join result?
4. How can a query find learners with no enrollment rows?
5. How can a filter in `WHERE` change the effect of a `LEFT JOIN`?
6. What does `CROSS JOIN` return?
7. How does `UNION` differ from `UNION ALL`?
8. What should be checked when a join unexpectedly increases the result count?

## References

- [Joins](https://learn.microsoft.com/sql/relational-databases/performance/joins)
- [FROM clause and JOIN syntax](https://learn.microsoft.com/sql/t-sql/queries/from-transact-sql)
- [UNION](https://learn.microsoft.com/sql/t-sql/language-elements/set-operators-union-transact-sql)
