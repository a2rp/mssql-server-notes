# 15. Security, backup, and restore

[Back to notes index](../README.md)

| [Previous: Transactions, locking, and isolation](./14-transactions-locking-and-isolation.md) | [Notes index](../README.md) | [Next: SQL Server with JavaScript](./16-sql-server-with-javascript.md) |
| --- | --- | --- |

## Use least privilege

SQL Server separates server logins from database users. A login authenticates to an instance. A database user maps that identity inside a database. Roles group permissions so an account receives only the access needed for its task.

If a login already exists, create a database user and grant read access only to the learning schema:

~~~sql
USE StudyNotes;
GO

CREATE USER app_reader FOR LOGIN app_reader;
GRANT SELECT ON SCHEMA::learning TO app_reader;
GO
~~~

Do not use `sysadmin` or `db_owner` for a normal application connection. Separate application identities from human operator accounts, review permissions periodically, and protect secrets outside source control.

## Protect connections and stored data

Use encrypted connections for network traffic and configure certificates correctly. Restrict network paths to approved clients. Protect backup files because they contain the data, even if the live database has strict permissions. Follow organizational requirements for encryption at rest, auditing, retention, and credential rotation.

## Create a full backup

A full database backup can be written to a path visible to the SQL Server service:

~~~sql
BACKUP DATABASE [StudyNotes]
TO DISK = N'D:\SqlBackups\StudyNotes_full.bak'
WITH INIT, CHECKSUM, COMPRESSION;
GO
~~~

The directory must exist and the SQL Server service identity must be able to write to it. `INIT` overwrites the backup media file, so use a unique destination or a controlled rotation policy. A backup stored only on the same disk as the database does not protect against disk loss.

## Check and restore a backup

`RESTORE VERIFYONLY` checks that the backup set is readable, but it does not prove that a complete database restore will succeed. Test restoration to a separate database on a regular schedule:

~~~sql
RESTORE VERIFYONLY
FROM DISK = N'D:\SqlBackups\StudyNotes_full.bak'
WITH CHECKSUM;
GO

RESTORE FILELISTONLY
FROM DISK = N'D:\SqlBackups\StudyNotes_full.bak';
GO
~~~

Use the logical file names returned by `RESTORE FILELISTONLY` in a test restore with `WITH MOVE` so the data and log files use paths reserved for the test copy. Confirm the restored database opens and the application can read expected records. Never restore over an important database as a first test.

## Understand recovery planning

A full backup contains the database at a point in time. Differential and transaction log backups can support more frequent recovery points when the configured recovery model and backup plan support them. Define recovery point and recovery time goals, retain copies away from the database host, and practice the restore sequence.

## Check what you learned

1. How does a server login differ from a database user?
2. Why should an application avoid `sysadmin` and `db_owner`?
3. Which permission does the example grant?
4. Which account needs access to the backup destination folder?
5. What does `WITH INIT` do?
6. What does `RESTORE VERIFYONLY` confirm, and what does it not prove?
7. Why restore a backup to a separate test database?
8. What do recovery point and recovery time goals help define?

## References

- [Database users and logins](https://learn.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine)
- [Permissions](https://learn.microsoft.com/sql/relational-databases/security/permissions-database-engine)
- [BACKUP DATABASE](https://learn.microsoft.com/sql/t-sql/statements/backup-transact-sql)
- [RESTORE statements](https://learn.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)
- [Backup and restore overview](https://learn.microsoft.com/sql/relational-databases/backup-restore/backup-and-restore-of-sql-server-databases)
