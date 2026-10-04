# Microsoft SQL Server Study Notes

These are my personal study notes from learning Microsoft SQL Server and working with relational data. They collect the core database ideas, T-SQL patterns, and application examples I want to understand and keep in one practical reference.

## About these notes

The chapters begin with database structure and SQL Server tools, then build through table design, queries, filtering, joins, grouping, data changes, constraints, transactions, indexes, security, backup and restore, and JavaScript application access. Each chapter explains the reason behind a feature, gives runnable examples, and ends with review questions.

Database examples use T-SQL. The application chapter uses JavaScript with the `mssql` package and parameterized queries. Run write examples against a local practice database first. SQL Server editions and tooling can differ, so use the official Microsoft documentation for current setup and compatibility details.

## Core topics

- Understand SQL Server databases, schemas, tables, rows, and data types
- Connect with SQL Server Management Studio and `sqlcmd`
- Create tables and relationships with keys and constraints
- Read data with `SELECT`, filters, sorting, and paging
- Combine data with joins, subqueries, and common table expressions
- Insert, update, and delete records safely
- Summarize data with aggregate functions and `GROUP BY`
- Use views, stored procedures, indexes, and execution plans
- Understand transactions, locking, and isolation levels
- Apply least-privilege access, backup, and restore practices
- Connect a JavaScript application using parameterized queries

## How I use these notes

I work through the chapters against a disposable `StudyNotes` database, run each statement, and inspect the result before changing it. I keep destructive commands away from important databases and use the review questions to check that I can explain each concept in my own words.

## Chapters

01. [SQL Server and relational database fundamentals](./chapters/01-sql-server-and-relational-fundamentals.md)
02. [Install tools and connect to SQL Server](./chapters/02-install-tools-and-connect.md)
03. [Databases, schemas, tables, and data types](./chapters/03-databases-schemas-tables-and-types.md)
04. [Read data with SELECT](./chapters/04-select-and-result-shapes.md)
05. [Filter rows with predicates](./chapters/05-filtering-and-predicates.md)
06. [Sort, page, and shape results](./chapters/06-sorting-pagination-and-projection.md)
07. [Insert, update, and delete data](./chapters/07-data-changes.md)
08. [Keys, constraints, and relationships](./chapters/08-keys-constraints-and-relationships.md)
09. [Joins and combining result sets](./chapters/09-joins-and-set-operations.md)
10. [Aggregate data with GROUP BY](./chapters/10-aggregation-and-grouping.md)
11. [Subqueries, CTEs, and window functions](./chapters/11-subqueries-ctes-and-window-functions.md)
12. [Views and stored procedures](./chapters/12-views-and-stored-procedures.md)
13. [Indexes and execution plans](./chapters/13-indexes-and-execution-plans.md)
14. [Transactions, locking, and isolation](./chapters/14-transactions-locking-and-isolation.md)
15. [Security, backup, and restore](./chapters/15-security-backup-and-restore.md)
16. [SQL Server with JavaScript](./chapters/16-sql-server-with-javascript.md)
98. [All code samples](./chapters/98-all-code-samples.md)
99. [Complete questions and answers](./chapters/99-complete-q-and-a.md)

## Official references

- [SQL Server documentation](https://learn.microsoft.com/sql/sql-server/)
- [T-SQL reference](https://learn.microsoft.com/sql/t-sql/language-reference)
- [Database design](https://learn.microsoft.com/sql/relational-databases/tables/primary-and-foreign-key-constraints)
- [Query processing architecture](https://learn.microsoft.com/sql/relational-databases/query-processing-architecture-guide)
- [Backup and restore](https://learn.microsoft.com/sql/relational-databases/backup-restore/)

## Links

- Portfolio: https://www.ashishranjan.net
- GitHub: https://github.com/a2rp
- CodePen: https://codepen.io/ash1198
- LinkedIn: https://www.linkedin.com/in/aashishranjan
- Facebook: https://www.facebook.com/theash.ashish/
- YouTube: https://www.youtube.com/@ashishranjan-ashz?sub_confirmation=1
- Email: mailto:ash.ranjan09@gmail.com

## Support

- Support: https://a2rp-donation-page.netlify.app/
- Buy Me a Coffee: https://buymeacoffee.com/ashishranjan
- Patreon: https://www.patreon.com/ashishranjan
