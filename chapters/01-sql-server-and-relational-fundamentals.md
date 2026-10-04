# 1. SQL Server and relational database fundamentals

[Back to notes index](../README.md)

| [Previous: Notes index](../README.md) | [Notes index](../README.md) | [Next: Install tools and connect to SQL Server](./02-install-tools-and-connect.md) |
| --- | --- | --- |

## What SQL Server does

Microsoft SQL Server is a relational database management system. It stores structured data and provides a language and engine for reading, changing, and protecting that data. An application or database tool sends a statement to the Database Engine, and SQL Server checks permissions, plans the work, and returns a result or status.

T-SQL, or Transact-SQL, is Microsoft's extension of SQL. It includes standard query and data-change statements along with SQL Server features for variables, error handling, procedures, and transactions.

## Relational structure

A SQL Server instance can contain databases. A database contains schemas, and schemas contain objects such as tables, views, procedures, and functions. Tables contain rows and columns:

| Term | Meaning |
| --- | --- |
| Instance | A running SQL Server service and its configuration |
| Database | A named store for related database objects and data |
| Schema | A namespace that groups objects and controls ownership |
| Table | A structured set of rows with named columns |
| Row | One record in a table |
| Column | One attribute with a declared data type |

A relational design uses keys and constraints to express relationships and protect data rules. Unlike a spreadsheet, a table's columns have declared types, and a database can reject values that violate its constraints.

## Create a practice database

Run this against a local development instance where you are allowed to create databases. `GO` is a batch separator understood by tools such as SQL Server Management Studio and `sqlcmd`; it is not a T-SQL statement sent to the Database Engine.

~~~sql
CREATE DATABASE StudyNotes;
GO

USE StudyNotes;
GO

CREATE TABLE dbo.Learners (
    LearnerId INT IDENTITY(1, 1) PRIMARY KEY,
    DisplayName NVARCHAR(100) NOT NULL,
    IsActive BIT NOT NULL DEFAULT 1,
    JoinedOn DATE NOT NULL DEFAULT CONVERT(date, GETDATE())
);
GO

INSERT INTO dbo.Learners (DisplayName)
VALUES (N'Mina Rao');
GO

SELECT LearnerId, DisplayName, IsActive, JoinedOn
FROM dbo.Learners;
GO
~~~

`dbo` is the default schema in many databases, but applications can define their own schemas. `IDENTITY` generates an increasing numeric value for inserted rows. `NOT NULL` requires a value, and `DEFAULT` supplies one when an insert omits that column.

## A statement is not the same as a result

A `SELECT` statement reads rows and returns a result set. An `INSERT`, `UPDATE`, or `DELETE` changes stored data and usually returns a count or status. A transaction can group multiple changes when the operation needs an all-or-nothing outcome.

Always know which database and instance a query targets. A familiar table name can exist in multiple databases, and the same write statement has different consequences depending on its target.

## Check what you learned

1. What kind of system is SQL Server?
2. What role does the Database Engine play when it receives a statement?
3. What is T-SQL?
4. Describe the relationship between an instance, database, schema, and table.
5. What does a column's data type describe?
6. What does `NOT NULL` require?
7. What does `GO` mean in tools such as SSMS and `sqlcmd`?
8. Why should you confirm the target database before running a write statement?

## References

- [SQL Server documentation](https://learn.microsoft.com/sql/sql-server/)
- [T-SQL language reference](https://learn.microsoft.com/sql/t-sql/language-reference)
- [Database Engine](https://learn.microsoft.com/sql/database-engine/)
- [Schemas and database objects](https://learn.microsoft.com/sql/relational-databases/security/authentication-access/ownership-and-user-schema-separation)
