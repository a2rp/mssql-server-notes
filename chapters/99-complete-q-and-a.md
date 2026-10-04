# 99. Complete questions and answers

[Back to notes index](../README.md)

| [Previous: All code samples](./98-all-code-samples.md) | [Notes index](../README.md) | [Next: Notes index](../README.md) |
| --- | --- | --- |

This appendix collects the review questions from the core chapters and gives a concise answer for each one.

## 1. SQL Server and relational database fundamentals

1. **Question:** What kind of system is SQL Server?  
   **Answer:** It is a relational database management system that stores structured data and processes queries and data changes.
2. **Question:** What role does the Database Engine play when it receives a statement?  
   **Answer:** It checks permissions, plans and executes the work, then returns rows or an operation status.
3. **Question:** What is T-SQL?  
   **Answer:** It is Microsoft's SQL language extension with query, data-change, procedural, and transaction features.
4. **Question:** Describe the relationship between an instance, database, schema, and table.  
   **Answer:** An instance hosts databases, a database contains schemas, and schemas contain tables and other objects.
5. **Question:** What does a column's data type describe?  
   **Answer:** It defines the kind of values stored in the column and affects valid operations and comparisons.
6. **Question:** What does `NOT NULL` require?  
   **Answer:** Every inserted row must have a non-null value for that column.
7. **Question:** What does `GO` mean in tools such as SSMS and `sqlcmd`?  
   **Answer:** It tells the client tool to send the current batch; it is not a T-SQL statement executed by the server.
8. **Question:** Why should you confirm the target database before running a write statement?  
   **Answer:** The same statement can change a different database than intended if the connection context is wrong.

## 2. Install tools and connect to SQL Server

1. **Question:** What does the Database Engine do compared with SSMS?  
   **Answer:** The Engine stores and processes data; SSMS is a client application used to connect and issue commands.
2. **Question:** Does installing SSMS by itself install the database server?  
   **Answer:** No. SSMS is a management client and does not itself install or start the Engine.
3. **Question:** What is the difference between a default instance and a named instance?  
   **Answer:** A default instance is addressed by the machine name alone, while a named instance includes its configured instance name.
4. **Question:** What does `-E` request from `sqlcmd`?  
   **Answer:** It requests Windows integrated authentication using the current account.
5. **Question:** Which database does the connection example target?  
   **Answer:** It connects to the `master` database.
6. **Question:** Name two things to check when a local connection fails.  
   **Answer:** Check that the Engine service is running and that the server or instance name is correct.
7. **Question:** Why should a SQL password not be placed directly in shell history?  
   **Answer:** Other users or tools may be able to inspect command history and recover the credential.
8. **Question:** What should you do when a certificate warning appears instead of disabling certificate checks?  
   **Answer:** Verify the server identity and configure a certificate that the client can trust.

## 3. Databases, schemas, tables, and data types

1. **Question:** What is a schema used for inside a database?  
   **Answer:** It groups objects in a namespace and can help organize ownership and permissions.
2. **Question:** Why qualify a table name with its schema?  
   **Answer:** It identifies the intended object clearly without relying on a user's default schema.
3. **Question:** Which type is designed for exact decimal values?  
   **Answer:** `DECIMAL(p, s)` stores exact decimal values with declared precision and scale.
4. **Question:** When is `DATE` more appropriate than `DATETIME2`?  
   **Answer:** Use `DATE` when only the calendar date matters and no time-of-day is needed.
5. **Question:** What do `NOT NULL` and `DEFAULT` each enforce?  
   **Answer:** `NOT NULL` requires a value; `DEFAULT` supplies a value when an insert omits the column.
6. **Question:** What does `IDENTITY` do?  
   **Answer:** It generates numeric values for inserted rows according to its seed and increment.
7. **Question:** What rule does a primary key provide?  
   **Answer:** It uniquely identifies each row and does not allow null key values.
8. **Question:** Why can adding a required column to a populated table need a default or staged migration?  
   **Answer:** Existing rows need a valid value to satisfy the new `NOT NULL` rule.

## 4. Read data with SELECT

1. **Question:** What does a `SELECT` statement return?  
   **Answer:** It returns a result set containing rows and columns produced by the query.
2. **Question:** Why is naming the needed columns preferable to `SELECT *` in application queries?  
   **Answer:** It avoids unnecessary data and keeps the result shape clear if the table changes.
3. **Question:** What does a column alias change?  
   **Answer:** It changes the output column's name for that query result, not the stored column.
4. **Question:** Does an expression in a `SELECT` list update the stored row?  
   **Answer:** No. It calculates an output value only.
5. **Question:** What is one use for returning a built-in function result?  
   **Answer:** A query can include the current UTC time or another calculated value in its result.
6. **Question:** What rows does `DISTINCT` remove?  
   **Answer:** It removes duplicate result rows based on all selected columns.
7. **Question:** Why might `DISTINCT` conceal a join problem?  
   **Answer:** It can hide duplicated rows caused by a missing or incorrect join condition.
8. **Question:** What helps keep an application-facing result shape stable?  
   **Answer:** Select explicit columns, use clear aliases, and avoid relying on every table column.

## 5. Filter rows with predicates

1. **Question:** What does `WHERE` determine in a query?  
   **Answer:** It determines which source rows qualify for the result.
2. **Question:** How do `AND` and `OR` combine predicates?  
   **Answer:** `AND` requires both predicates to be true, while `OR` accepts either true predicate.
3. **Question:** Why add parentheses around mixed `AND` and `OR` conditions?  
   **Answer:** Parentheses make the intended grouping explicit and avoid relying on precedence assumptions.
4. **Question:** What does `BETWEEN` do with its endpoints?  
   **Answer:** It includes both the lower and upper endpoint values.
5. **Question:** Which wildcards represent many characters and one character in `LIKE`?  
   **Answer:** `%` matches any sequence of characters, and `_` matches one character.
6. **Question:** Why does `Email = NULL` not find missing email values?  
   **Answer:** Comparisons with `NULL` evaluate to unknown; use `IS NULL` instead.
7. **Question:** What is a half-open date range?  
   **Answer:** It includes the start value and excludes the next boundary, such as `>=` one date and `<` the next date.
8. **Question:** Why can a leading wildcard make an indexed text search less efficient?  
   **Answer:** The engine may be unable to seek directly to a known beginning of the indexed text value.

## 6. Sort, page, and shape results

1. **Question:** When is a query's row order guaranteed?  
   **Answer:** Only when the query specifies an `ORDER BY` clause.
2. **Question:** Why add a unique tie-breaker to an order?  
   **Answer:** It makes rows with equal primary sort values appear in a deterministic order.
3. **Question:** What does `TOP (10)` limit?  
   **Answer:** It limits the result to at most ten rows.
4. **Question:** Why should `TOP` usually be paired with `ORDER BY`?  
   **Answer:** Ordering defines which rows are the first ones selected.
5. **Question:** Which clauses are needed for `OFFSET` and `FETCH NEXT`?  
   **Answer:** They are used with `ORDER BY` to define a row order and page range.
6. **Question:** Why validate page size in an application?  
   **Answer:** It prevents callers from requesting an excessively large result set.
7. **Question:** How does keyset paging decide where to begin the next page?  
   **Answer:** It filters after the final key returned by the previous page.
8. **Question:** What four query choices make an API result easier to control?  
   **Answer:** Use a filter, stable sort, explicit projection, and maximum row count.

## 7. Insert, update, and delete data

1. **Question:** Why name insert columns explicitly?  
   **Answer:** It documents the intended values and prevents the insert from depending on table column order.
2. **Question:** How can an insert return a generated identity value?  
   **Answer:** Use the `OUTPUT inserted.ColumnName` clause.
3. **Question:** What can prevent an insert from storing a duplicate email?  
   **Answer:** A unique constraint or unique index on the email column.
4. **Question:** What is the risk of an `UPDATE` without a `WHERE` clause?  
   **Answer:** It updates every row in the target table.
5. **Question:** What should be checked before deleting rows?  
   **Answer:** Preview the same filter with a `SELECT` and verify the target records.
6. **Question:** What does `ROLLBACK` do in the practice example?  
   **Answer:** It undoes the uncommitted delete in the open transaction.
7. **Question:** Why are application values sent as parameters?  
   **Answer:** Parameters keep values separate from SQL syntax and help prevent injection.
8. **Question:** When might several data changes need a transaction?  
   **Answer:** When they represent one operation that must either fully succeed or fully fail.

## 8. Keys, constraints, and relationships

1. **Question:** What does a primary key guarantee?  
   **Answer:** It provides a unique, non-null identifier for each row.
2. **Question:** How does a unique constraint differ from a primary key?  
   **Answer:** It enforces uniqueness on another column or combination without being the table's primary identifier.
3. **Question:** What is a surrogate key?  
   **Answer:** It is an artificial identifier, such as an identity integer, created to identify a row.
4. **Question:** What does a foreign key check?  
   **Answer:** It ensures the referenced parent key exists for a child row.
5. **Question:** How does the enrollment table represent a many-to-many relationship?  
   **Answer:** Each row connects one learner to one course, allowing either side to have many related records.
6. **Question:** What rule does the composite key on `Enrollments` enforce?  
   **Answer:** It prevents the same learner and course pair from appearing more than once.
7. **Question:** How do `CHECK` and `DEFAULT` constraints differ?  
   **Answer:** `CHECK` rejects values outside a rule; `DEFAULT` supplies a value when none is provided.
8. **Question:** Why should cascading deletes be chosen carefully?  
   **Answer:** Deleting one parent can automatically remove many child rows.

## 9. Joins and combining result sets

1. **Question:** Which rows does an `INNER JOIN` return?  
   **Answer:** Only rows with a match on both sides of its join condition.
2. **Question:** Which input's rows are preserved by a `LEFT JOIN`?  
   **Answer:** Every row from its left input is preserved.
3. **Question:** Why might one learner appear several times in a join result?  
   **Answer:** A learner with multiple enrollment rows produces one joined result row for each match.
4. **Question:** How can a query find learners with no enrollment rows?  
   **Answer:** Use a left join and filter for a null value in a right-side non-nullable key.
5. **Question:** How can a filter in `WHERE` change the effect of a `LEFT JOIN`?  
   **Answer:** A right-side condition in `WHERE` can remove null-extended rows and effectively discard unmatched learners.
6. **Question:** What does `CROSS JOIN` return?  
   **Answer:** It returns every combination of a row from each input.
7. **Question:** How does `UNION` differ from `UNION ALL`?  
   **Answer:** `UNION` removes duplicate result rows; `UNION ALL` preserves them.
8. **Question:** What should be checked when a join unexpectedly increases the result count?  
   **Answer:** Check the join predicate and the number of matches on each side, since an incomplete condition can multiply rows.

## 10. Aggregate data with GROUP BY

1. **Question:** What does an aggregate function calculate?  
   **Answer:** It calculates a summary value from one or more input rows.
2. **Question:** How do `COUNT(*)` and `COUNT(ColumnName)` differ?  
   **Answer:** `COUNT(*)` counts rows; `COUNT(ColumnName)` counts only rows where the expression is not null.
3. **Question:** What does `GROUP BY` determine?  
   **Answer:** It determines which source rows are combined into each aggregate result row.
4. **Question:** Which selected columns must appear in `GROUP BY`?  
   **Answer:** Every selected column that is not itself aggregated must be grouped.
5. **Question:** How do `WHERE` and `HAVING` differ?  
   **Answer:** `WHERE` filters rows before grouping; `HAVING` filters grouped results.
6. **Question:** What does `COUNT(DISTINCT CourseId)` count?  
   **Answer:** It counts distinct non-null course identifiers.
7. **Question:** How can a join affect an aggregate's input rows?  
   **Answer:** A one-to-many join repeats parent values for each child match, which can inflate a sum or count.
8. **Question:** Why should the intended reporting grain be clear before grouping?  
   **Answer:** It defines what one output row represents and prevents summarizing at the wrong level.

## 11. Subqueries, CTEs, and window functions

1. **Question:** What is a subquery?  
   **Answer:** It is a query nested inside another SQL statement.
2. **Question:** What requirement applies to a scalar subquery in a comparison?  
   **Answer:** It must return one scalar value for that use.
3. **Question:** What question does `EXISTS` answer?  
   **Answer:** It checks whether a related query returns at least one row.
4. **Question:** How can `NOT EXISTS` find rows with no related records?  
   **Answer:** Correlate a subquery to each outer row and retain rows for which no matching child exists.
5. **Question:** How long is a CTE available?  
   **Answer:** It is available to the single statement immediately following its definition.
6. **Question:** Does a CTE automatically cache its result?  
   **Answer:** No. It names a query expression and does not itself guarantee stored or cached results.
7. **Question:** How does a window function differ from a grouped aggregate?  
   **Answer:** A window function calculates across related rows while keeping each input row in the output.
8. **Question:** Why may a window function need an outer `ORDER BY` as well?  
   **Answer:** The ordering inside `OVER` controls the calculation, not the final display order.

## 12. Views and stored procedures

1. **Question:** What does a regular view store?  
   **Answer:** It stores a query definition, not a separate copy of the result rows.
2. **Question:** Give one reason to expose a view instead of a base table.  
   **Answer:** A view can provide a limited, stable result shape or hide repeated query details.
3. **Question:** What can happen when a view's underlying table changes?  
   **Answer:** The view may return an error or no longer represent the intended result, so dependencies need review.
4. **Question:** Why should stored procedure parameters have explicit types?  
   **Answer:** Explicit types document the interface and avoid unintended conversions or mismatches.
5. **Question:** How can a stored procedure be called with a named argument?  
   **Answer:** Use an invocation such as `EXEC learning.FindLearners @IsActive = 1`.
6. **Question:** Why should values not be concatenated into dynamic SQL?  
   **Answer:** It can allow input to alter the statement and create SQL injection vulnerabilities.
7. **Question:** When might a function be a better fit than a procedure?  
   **Answer:** Use a function when reusable logic naturally returns a value or table expression.
8. **Question:** What should be clear about the responsibility of a database object?  
   **Answer:** It should have a focused purpose and a clearly defined interface and permission model.

## 13. Indexes and execution plans

1. **Question:** What work can an index help SQL Server avoid?  
   **Answer:** It can avoid reading every table row to find matches or produce a useful order.
2. **Question:** How do clustered and nonclustered indexes differ?  
   **Answer:** A clustered rowstore index organizes the table's data pages by its key; a nonclustered index stores keys and row locators separately.
3. **Question:** What does an included column contribute to a nonclustered index?  
   **Answer:** It stores an extra output value at the leaf level so a query may be covered without adding it to the key order.
4. **Question:** What constraint can a unique index enforce?  
   **Answer:** It can prevent duplicate values or duplicate key combinations.
5. **Question:** Why is an index scan not automatically a problem?  
   **Answer:** Scanning can be efficient when a query needs a large share of the index or table.
6. **Question:** Which measurements can help compare query work?  
   **Answer:** Compare logical reads, duration, estimated and actual row counts, and the full execution plan.
7. **Question:** Why can `YEAR(JoinedAt) = 2026` make index use harder?  
   **Answer:** Applying a function to the indexed column can prevent a direct range seek on its stored values.
8. **Question:** Why should indexes be added based on measured queries?  
   **Answer:** Indexes cost storage and add work to every data change, so each one should support a real workload.

## 14. Transactions, locking, and isolation

1. **Question:** What does a transaction group together?  
   **Answer:** It groups statements whose changes should commit or roll back as one unit.
2. **Question:** Expand the ACID properties.  
   **Answer:** Atomicity, consistency, isolation, and durability.
3. **Question:** What is the purpose of `XACT_STATE()` in the error handler?  
   **Answer:** It reports whether the transaction is active and whether it can still commit, guiding rollback handling.
4. **Question:** How does a long transaction affect other work?  
   **Answer:** It can hold locks or row versions longer and block or slow other operations.
5. **Question:** What is a deadlock?  
   **Answer:** It is a cycle where transactions wait for resources held by one another.
6. **Question:** What does SQL Server do to resolve a deadlock cycle?  
   **Answer:** It selects a victim transaction and rolls it back so the other can continue.
7. **Question:** How can isolation level affect blocking and data visibility?  
   **Answer:** It determines which concurrent changes a transaction can see and what coordination or row versioning is needed.
8. **Question:** Why should database settings be checked instead of assuming a default behavior?  
   **Answer:** Options such as read-committed snapshot can vary by database and change concurrency behavior.

## 15. Security, backup, and restore

1. **Question:** How does a server login differ from a database user?  
   **Answer:** A login authenticates to the SQL Server instance; a database user represents that identity inside one database.
2. **Question:** Why should an application avoid `sysadmin` and `db_owner`?  
   **Answer:** Those roles grant far more authority than a normal application needs.
3. **Question:** Which permission does the example grant?  
   **Answer:** It grants `SELECT` permission on the `learning` schema.
4. **Question:** Which account needs access to the backup destination folder?  
   **Answer:** The SQL Server service identity must be able to write to the destination path.
5. **Question:** What does `WITH INIT` do?  
   **Answer:** It overwrites the existing backup media set at the specified destination.
6. **Question:** What does `RESTORE VERIFYONLY` confirm, and what does it not prove?  
   **Answer:** It checks backup readability and structure but does not prove a full restore will succeed.
7. **Question:** Why restore a backup to a separate test database?  
   **Answer:** It tests recovery without overwriting the source or another important database.
8. **Question:** What do recovery point and recovery time goals help define?  
   **Answer:** They define how much data loss is acceptable and how quickly service should be restored.

## 16. SQL Server with JavaScript

1. **Question:** What package does this chapter use to access SQL Server from JavaScript?  
   **Answer:** It uses the `mssql` npm package.
2. **Question:** Why should an application reuse a connection pool?  
   **Answer:** Reusing the pool avoids opening and managing new database connections for every request.
3. **Question:** Where should database credentials be stored?  
   **Answer:** Store them in protected environment configuration or a secrets manager.
4. **Question:** How does `.input()` help protect a query value?  
   **Answer:** It binds a value as a parameter, keeping it separate from the SQL statement text.
5. **Question:** Why should the JavaScript parameter type match the SQL column type?  
   **Answer:** Matching types avoids unintended conversions and preserves expected comparison behavior.
6. **Question:** Can a value parameter stand in for a table name?  
   **Answer:** No. Parameters bind values, not SQL identifiers such as table or column names.
7. **Question:** When should a one-off script close its pool?  
   **Answer:** It should close the pool when its work finishes, including when an operation throws.
8. **Question:** What should an application avoid returning in a public error response?  
   **Answer:** It should not expose credentials, sensitive data, raw database messages, or internal stack traces.
