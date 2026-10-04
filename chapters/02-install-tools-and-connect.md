# 2. Install tools and connect to SQL Server

[Back to notes index](../README.md)

| [Previous: SQL Server and relational database fundamentals](./01-sql-server-and-relational-fundamentals.md) | [Notes index](../README.md) | [Next: Databases, schemas, tables, and data types](./03-databases-schemas-tables-and-types.md) |
| --- | --- | --- |

## Separate the server from its tools

SQL Server Database Engine stores and processes data. SQL Server Management Studio (SSMS) is a graphical client for connecting to a server and working with database objects. `sqlcmd` is a command-line client that can run statements and scripts. Installing a client does not automatically install or start a Database Engine instance.

For a local development machine, choose a SQL Server edition and installation method that match your use. Developer editions are intended for development and testing, not production workloads. Follow the current Microsoft setup instructions because edition names, downloads, and supported operating systems can change.

## Connect with SQL Server Management Studio

1. Open SSMS and choose **Connect** then **Database Engine**.
2. Enter the server or instance name from the SQL Server installation.
3. Choose the authentication mode configured for that instance.
4. Connect and confirm the server name shown in Object Explorer.
5. Open a new query window and check the selected database before running statements.

A default local instance may accept `localhost`. A named Express instance is often installed under a name such as `localhost\SQLEXPRESS`. Use the actual instance name shown by your installation.

## Connect with sqlcmd

With Windows authentication, `-E` uses the current Windows account. This example connects to a named local instance and runs a read-only query:

~~~powershell
sqlcmd -S ".\SQLEXPRESS" -E -d master -Q "SELECT @@SERVERNAME AS ServerName, DB_NAME() AS DatabaseName;"
~~~

For a default instance, the server name may be `localhost`. For a LocalDB installation, the instance name is commonly `(localdb)\MSSQLLocalDB`. Connection options vary with the installed `sqlcmd` version and server configuration.

Avoid putting SQL passwords directly in command history or scripts. Prefer integrated authentication for local development when available. For remote connections, use the authentication and encryption settings required by the organization. Do not disable certificate checks as a shortcut for a connection warning.

## Check the connection

After connecting, run a small query that reports the server and database context:

~~~sql
SELECT
    @@SERVERNAME AS ServerName,
    DB_NAME() AS CurrentDatabase,
    ORIGINAL_LOGIN() AS LoginName;
GO
~~~

If the connection fails, check that the Database Engine service is running, the instance name is correct, the login is allowed, and network or firewall settings permit the connection. For a remote server, confirm the server's encryption and certificate setup instead of weakening the client settings.

## Check what you learned

1. What does the Database Engine do compared with SSMS?
2. Does installing SSMS by itself install the database server?
3. What is the difference between a default instance and a named instance?
4. What does `-E` request from `sqlcmd`?
5. Which database does the connection example target?
6. Name two things to check when a local connection fails.
7. Why should a SQL password not be placed directly in shell history?
8. What should you do when a certificate warning appears instead of disabling certificate checks?

## References

- [Install SQL Server](https://learn.microsoft.com/sql/database-engine/install-windows/install-sql-server)
- [Install SQL Server Management Studio](https://learn.microsoft.com/ssms/install/install)
- [sqlcmd utility](https://learn.microsoft.com/sql/tools/sqlcmd/sqlcmd-utility)
- [Connect to the Database Engine](https://learn.microsoft.com/sql/ssms/quickstarts/ssms-connect-query-sql-server)
