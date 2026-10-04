# 3. Databases, schemas, tables, and data types

[Back to notes index](../README.md)

| [Previous: Install tools and connect to SQL Server](./02-install-tools-and-connect.md) | [Notes index](../README.md) | [Next: Read data with SELECT](./04-select-and-result-shapes.md) |
| --- | --- | --- |

## Create a named schema

A schema is a namespace inside a database. It groups objects and can be used to grant permissions. Qualify object names with the schema, such as `learning.Learners`, so queries do not rely on an implicit default.

~~~sql
USE StudyNotes;
GO

CREATE SCHEMA learning;
GO
~~~

The database may already have schemas such as `dbo`. Create a separate schema when it helps organize related objects or set permissions by area.

## Choose column types intentionally

A column's type controls which values it can store and which operations make sense:

| Type | Common use |
| --- | --- |
| `INT` / `BIGINT` | Whole numbers and identifiers |
| `DECIMAL(p, s)` | Exact decimal values such as amounts |
| `BIT` | A Boolean-like true/false value |
| `NVARCHAR(n)` | Unicode text with a maximum length |
| `DATE` | A calendar date without a time |
| `DATETIME2` | A date and time with fractional-second precision |
| `UNIQUEIDENTIFIER` | A GUID value |

Choose a type that fits the domain and expected range. Avoid using text for values that should be compared numerically or as dates. Use `DECIMAL` for exact decimal arithmetic such as stored currency amounts, and define its precision and scale deliberately.

## Create a table with rules

~~~sql
CREATE TABLE learning.Learners (
    LearnerId INT IDENTITY(1, 1) NOT NULL,
    DisplayName NVARCHAR(100) NOT NULL,
    Email NVARCHAR(254) NOT NULL,
    Balance DECIMAL(12, 2) NOT NULL DEFAULT 0,
    IsActive BIT NOT NULL DEFAULT 1,
    JoinedAt DATETIME2(0) NOT NULL DEFAULT SYSUTCDATETIME(),
    CONSTRAINT PK_Learners PRIMARY KEY (LearnerId),
    CONSTRAINT UQ_Learners_Email UNIQUE (Email),
    CONSTRAINT CK_Learners_Balance_NonNegative CHECK (Balance >= 0)
);
GO
~~~

`IDENTITY` generates a number when a row is inserted. `NOT NULL` requires a value. `DEFAULT` supplies a value when an insert omits the column. A primary key uniquely identifies a row. The named constraints describe the rules the database enforces.

## Change a table carefully

`ALTER TABLE` changes a table definition. When adding a required column to a table that already contains rows, provide a default or a staged migration so existing records can satisfy the new rule:

~~~sql
ALTER TABLE learning.Learners
ADD PreferredLanguage NVARCHAR(20) NOT NULL
    CONSTRAINT DF_Learners_PreferredLanguage DEFAULT N'en';
GO
~~~

Inspect table definitions in SSMS or query system catalog views such as `sys.tables`, `sys.schemas`, and `sys.columns`. Before changing production data structures, test the migration against a copy and check the affected application code.

## Check what you learned

1. What is a schema used for inside a database?
2. Why qualify a table name with its schema?
3. Which type is designed for exact decimal values?
4. When is `DATE` more appropriate than `DATETIME2`?
5. What do `NOT NULL` and `DEFAULT` each enforce?
6. What does `IDENTITY` do?
7. What rule does a primary key provide?
8. Why can adding a required column to a populated table need a default or staged migration?

## References

- [Create tables](https://learn.microsoft.com/sql/relational-databases/tables/create-tables-database-engine)
- [Data types](https://learn.microsoft.com/sql/t-sql/data-types/data-types-transact-sql)
- [Schemas and users](https://learn.microsoft.com/sql/relational-databases/security/authentication-access/ownership-and-user-schema-separation)
- [ALTER TABLE](https://learn.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)
