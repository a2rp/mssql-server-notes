# 6. Sort, page, and shape results

[Back to notes index](../README.md)

| [Previous: Filter rows with predicates](./05-filtering-and-predicates.md) | [Notes index](../README.md) | [Next: Insert, update, and delete data](./07-data-changes.md) |
| --- | --- | --- |

## Order rows explicitly

A result has no guaranteed order unless the query includes `ORDER BY`. Sort in ascending order with `ASC` or descending order with `DESC`:

~~~sql
SELECT LearnerId, DisplayName, JoinedAt
FROM learning.Learners
ORDER BY JoinedAt DESC, LearnerId ASC;
GO
~~~

Add a unique tie-breaker such as the primary key when the main sort field can repeat. This makes paging results stable across requests when the data is unchanged.

## Return a bounded first page

`TOP` limits the number of rows. Pair it with an order when the specific rows matter:

~~~sql
SELECT TOP (10) LearnerId, DisplayName, Balance
FROM learning.Learners
WHERE IsActive = 1
ORDER BY Balance DESC, LearnerId ASC;
GO
~~~

Without `ORDER BY`, SQL Server may return any qualifying rows. `TOP` is not a substitute for paging when the caller needs later pages.

## Page with OFFSET and FETCH

Use `ORDER BY` with `OFFSET` and `FETCH NEXT` to request a page:

~~~sql
DECLARE @PageNumber INT = 2;
DECLARE @PageSize INT = 20;
DECLARE @Offset INT = (@PageNumber - 1) * @PageSize;

SELECT LearnerId, DisplayName, JoinedAt
FROM learning.Learners
ORDER BY LearnerId
OFFSET @Offset ROWS
FETCH NEXT @PageSize ROWS ONLY;
GO
~~~

Validate that the page number and page size are positive, and set a maximum page size in the application. Large offsets can become expensive because the engine may still need to walk past earlier rows.

## Use keyset paging for sequential navigation

For a table ordered by its increasing key, the next page can start after the last key returned by the previous page:

~~~sql
DECLARE @LastLearnerId INT = 120;
DECLARE @PageSize INT = 20;

SELECT TOP (@PageSize) LearnerId, DisplayName
FROM learning.Learners
WHERE LearnerId > @LastLearnerId
ORDER BY LearnerId;
GO
~~~

The application stores the last key from the current page and sends it with the next request. For a compound order, carry each sort value and use a filter that follows the same order.

## Keep result shapes clear

Select only the columns the caller needs and alias calculated values with useful names. The combination of a filter, a stable order, a projection, and a maximum row count makes a query's output easier to use and safer to expose through an API.

## Check what you learned

1. When is a query's row order guaranteed?
2. Why add a unique tie-breaker to an order?
3. What does `TOP (10)` limit?
4. Why should `TOP` usually be paired with `ORDER BY`?
5. Which clauses are needed for `OFFSET` and `FETCH NEXT`?
6. Why validate page size in an application?
7. How does keyset paging decide where to begin the next page?
8. What four query choices make an API result easier to control?

## References

- [ORDER BY clause](https://learn.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)
- [TOP clause](https://learn.microsoft.com/sql/t-sql/queries/top-transact-sql)
- [OFFSET and FETCH](https://learn.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql#offset-and-fetch)
