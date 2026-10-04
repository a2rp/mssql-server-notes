# 5. Filter rows with predicates

[Back to notes index](../README.md)

| [Previous: Read data with SELECT](./04-select-and-result-shapes.md) | [Notes index](../README.md) | [Next: Sort, page, and shape results](./06-sorting-pagination-and-projection.md) |
| --- | --- | --- |

## Filter with WHERE

A `WHERE` clause keeps rows whose predicate evaluates to true:

~~~sql
SELECT LearnerId, DisplayName, Balance
FROM learning.Learners
WHERE IsActive = 1;
GO
~~~

Common comparison operators include `=`, `<>`, `>`, `>=`, `<`, and `<=`. Use values of a compatible type, and use `N` before a Unicode string literal when the value contains Unicode text.

## Combine conditions clearly

`AND` requires both conditions to be true. `OR` accepts either condition. Use parentheses to make mixed logic explicit:

~~~sql
SELECT LearnerId, DisplayName, Balance
FROM learning.Learners
WHERE IsActive = 1
  AND (Balance < 100 OR DisplayName = N'Mina Rao');
GO
~~~

Without parentheses, operators follow precedence rules that may not match the intended grouping. Clear parentheses help the next reader verify the logic.

## Use IN, BETWEEN, and LIKE

`IN` checks a value against a list. `BETWEEN` includes both endpoints. `LIKE` matches a text pattern:

~~~sql
SELECT DisplayName
FROM learning.Learners
WHERE DisplayName IN (N'Mina Rao', N'Ravi Das');
GO

SELECT DisplayName, Balance
FROM learning.Learners
WHERE Balance BETWEEN 50 AND 200;
GO

SELECT DisplayName
FROM learning.Learners
WHERE DisplayName LIKE N'M%';
GO
~~~

In a SQL Server `LIKE` pattern, `%` matches zero or more characters and `_` matches one character. Bracket expressions can match a character range or set. A leading wildcard such as `N'%ina'` often makes it harder to use a conventional index efficiently.

## Handle NULL explicitly

`NULL` represents an unknown or missing value. It is not equal to zero, an empty string, or another `NULL`. Use `IS NULL` and `IS NOT NULL`:

~~~sql
SELECT LearnerId, DisplayName
FROM learning.Learners
WHERE Email IS NULL;
GO
~~~

Comparisons involving `NULL` evaluate to unknown rather than true or false. This is why `Email = NULL` does not find missing values. SQL Server's three-valued logic also means `NOT` conditions need deliberate handling when nullable columns are involved.

## Filter a date range safely

For a date-time column, a half-open range includes every time on the first date and excludes the next day:

~~~sql
SELECT LearnerId, JoinedAt
FROM learning.Learners
WHERE JoinedAt >= '2026-01-01'
  AND JoinedAt <  '2027-01-01';
GO
~~~

This avoids guessing the last time value of a day. Match the literal to the column type and time-zone convention used by the application.

## Check what you learned

1. What does `WHERE` determine in a query?
2. How do `AND` and `OR` combine predicates?
3. Why add parentheses around mixed `AND` and `OR` conditions?
4. What does `BETWEEN` do with its endpoints?
5. Which wildcards represent many characters and one character in `LIKE`?
6. Why does `Email = NULL` not find missing email values?
7. What is a half-open date range?
8. Why can a leading wildcard make an indexed text search less efficient?

## References

- [WHERE clause](https://learn.microsoft.com/sql/t-sql/queries/where-transact-sql)
- [LIKE](https://learn.microsoft.com/sql/t-sql/language-elements/like-transact-sql)
- [NULL and UNKNOWN](https://learn.microsoft.com/sql/t-sql/language-elements/null-and-unknown-transact-sql)
- [BETWEEN](https://learn.microsoft.com/sql/t-sql/language-elements/between-transact-sql)
