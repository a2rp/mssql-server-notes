# 16. SQL Server with JavaScript

[Back to notes index](../README.md)

| [Previous: Security, backup, and restore](./15-security-backup-and-restore.md) | [Notes index](../README.md) | [Next: All code samples](./98-all-code-samples.md) |
| --- | --- | --- |

## Install a Node.js database package

Node.js applications connect to SQL Server through a database driver. The `mssql` npm package provides a JavaScript API and uses a driver such as Tedious to communicate with SQL Server. Check its current compatibility and authentication support before choosing it for a project.

~~~sh
npm install mssql
~~~

Keep database credentials in environment configuration or a secrets manager. For a local PowerShell session, set environment variables before starting the app:

~~~powershell
$env:SQL_SERVER = "localhost"
$env:SQL_DATABASE = "StudyNotes"
$env:SQL_USER = "app_reader"
$env:SQL_PASSWORD = "<local-secret>"
~~~

Do not place a real password in source code, command examples, screenshots, or committed configuration.

## Open a connection pool

Create a pool during application startup and reuse it for database operations. This example uses encrypted transport and expects the server certificate to be trusted:

~~~javascript
import sql from "mssql"

const config = {
  server: process.env.SQL_SERVER,
  database: process.env.SQL_DATABASE,
  user: process.env.SQL_USER,
  password: process.env.SQL_PASSWORD,
  options: {
    encrypt: true,
    trustServerCertificate: false
  }
}

const pool = await sql.connect(config)
~~~

Certificate configuration depends on the server and deployment. Fix local trust configuration using a development certificate rather than disabling certificate checks in production. Use integrated authentication only with a driver and setup that explicitly support the required Windows or cloud identity flow.

## Send values as parameters

Bind application values with `.input()` instead of joining them into SQL text:

~~~javascript
const learnerId = 1

const result = await pool.request()
  .input("LearnerId", sql.Int, learnerId)
  .query(`
    SELECT LearnerId, DisplayName, Email
    FROM learning.Learners
    WHERE LearnerId = @LearnerId
  `)

console.log(result.recordset)
~~~

The parameter type should match the SQL column type. Parameters protect values from being treated as SQL syntax. They do not replace authorization checks, and they cannot be used as placeholders for table or column names.

## Insert a row with a parameter

~~~javascript
const displayName = "Mina Rao"
const email = "mina@example.com"

const result = await pool.request()
  .input("DisplayName", sql.NVarChar(100), displayName)
  .input("Email", sql.NVarChar(254), email)
  .query(`
    INSERT INTO learning.Learners (DisplayName, Email)
    OUTPUT inserted.LearnerId, inserted.DisplayName
    VALUES (@DisplayName, @Email)
  `)

console.log(result.recordset[0])
~~~

The driver returns rows from the `OUTPUT` clause in `recordset`. Catch constraint and connection errors at the application boundary and return a useful response without exposing secrets or raw internal details.

## Manage the pool lifetime

A one-off script should close the pool in a `finally` block. A web service should keep a shared pool available during its lifetime and close it during graceful shutdown. Do not open a new pool for every incoming request.

~~~javascript
try {
  const result = await pool.request().query("SELECT DB_NAME() AS DatabaseName")
  console.log(result.recordset[0].DatabaseName)
} finally {
  await pool.close()
}
~~~

## Check what you learned

1. What package does this chapter use to access SQL Server from JavaScript?
2. Why should an application reuse a connection pool?
3. Where should database credentials be stored?
4. How does `.input()` help protect a query value?
5. Why should the JavaScript parameter type match the SQL column type?
6. Can a value parameter stand in for a table name?
7. When should a one-off script close its pool?
8. What should an application avoid returning in a public error response?

## References

- [Microsoft Node.js driver overview](https://learn.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server)
- [Microsoft Node.js connection example](https://learn.microsoft.com/sql/connect/node-js/step-3-proof-of-concept-connecting-to-sql-using-node-js)
- [node-mssql package and documentation](https://github.com/tediousjs/node-mssql)
- [Tedious driver](https://github.com/tediousjs/tedious)
