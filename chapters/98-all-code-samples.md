# 98. All code samples

[Back to notes index](../README.md)

| [Previous: SQL Server with JavaScript](./16-sql-server-with-javascript.md) | [Notes index](../README.md) | [Next: Complete questions and answers](./99-complete-q-and-a.md) |
| --- | --- | --- |

## SQL Server and relational database fundamentals

Source: [Open chapter](./01-sql-server-and-relational-fundamentals.md)

### Example 1

~~~~sql
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
~~~~

## Install tools and connect to SQL Server

Source: [Open chapter](./02-install-tools-and-connect.md)

### Example 1

~~~~powershell
sqlcmd -S ".\SQLEXPRESS" -E -d master -Q "SELECT @@SERVERNAME AS ServerName, DB_NAME() AS DatabaseName;"
~~~~

### Example 2

~~~~sql
SELECT
    @@SERVERNAME AS ServerName,
    DB_NAME() AS CurrentDatabase,
    ORIGINAL_LOGIN() AS LoginName;
GO
~~~~

## Databases, schemas, tables, and data types

Source: [Open chapter](./03-databases-schemas-tables-and-types.md)

### Example 1

~~~~sql
USE StudyNotes;
GO

CREATE SCHEMA learning;
GO
~~~~

### Example 2

~~~~sql
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
~~~~

### Example 3

~~~~sql
ALTER TABLE learning.Learners
ADD PreferredLanguage NVARCHAR(20) NOT NULL
    CONSTRAINT DF_Learners_PreferredLanguage DEFAULT N'en';
GO
~~~~

## Read data with SELECT

Source: [Open chapter](./04-select-and-result-shapes.md)

### Example 1

~~~~sql
SELECT LearnerId, DisplayName, Email
FROM learning.Learners;
GO
~~~~

### Example 2

~~~~sql
SELECT
    DisplayName AS LearnerName,
    Balance,
    Balance * 0.10 AS EstimatedTenPercent
FROM learning.Learners;
GO
~~~~

### Example 3

~~~~sql
SELECT
    LearnerId,
    DisplayName,
    IsActive,
    SYSUTCDATETIME() AS ReadAtUtc
FROM learning.Learners;
GO
~~~~

### Example 4

~~~~sql
SELECT DISTINCT IsActive
FROM learning.Learners;
GO
~~~~

## Filter rows with predicates

Source: [Open chapter](./05-filtering-and-predicates.md)

### Example 1

~~~~sql
SELECT LearnerId, DisplayName, Balance
FROM learning.Learners
WHERE IsActive = 1;
GO
~~~~

### Example 2

~~~~sql
SELECT LearnerId, DisplayName, Balance
FROM learning.Learners
WHERE IsActive = 1
  AND (Balance < 100 OR DisplayName = N'Mina Rao');
GO
~~~~

### Example 3

~~~~sql
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
~~~~

### Example 4

~~~~sql
SELECT LearnerId, DisplayName
FROM learning.Learners
WHERE Email IS NULL;
GO
~~~~

### Example 5

~~~~sql
SELECT LearnerId, JoinedAt
FROM learning.Learners
WHERE JoinedAt >= '2026-01-01'
  AND JoinedAt <  '2027-01-01';
GO
~~~~

## Sort, page, and shape results

Source: [Open chapter](./06-sorting-pagination-and-projection.md)

### Example 1

~~~~sql
SELECT LearnerId, DisplayName, JoinedAt
FROM learning.Learners
ORDER BY JoinedAt DESC, LearnerId ASC;
GO
~~~~

### Example 2

~~~~sql
SELECT TOP (10) LearnerId, DisplayName, Balance
FROM learning.Learners
WHERE IsActive = 1
ORDER BY Balance DESC, LearnerId ASC;
GO
~~~~

### Example 3

~~~~sql
DECLARE @PageNumber INT = 2;
DECLARE @PageSize INT = 20;
DECLARE @Offset INT = (@PageNumber - 1) * @PageSize;

SELECT LearnerId, DisplayName, JoinedAt
FROM learning.Learners
ORDER BY LearnerId
OFFSET @Offset ROWS
FETCH NEXT @PageSize ROWS ONLY;
GO
~~~~

### Example 4

~~~~sql
DECLARE @LastLearnerId INT = 120;
DECLARE @PageSize INT = 20;

SELECT TOP (@PageSize) LearnerId, DisplayName
FROM learning.Learners
WHERE LearnerId > @LastLearnerId
ORDER BY LearnerId;
GO
~~~~

## Insert, update, and delete data

Source: [Open chapter](./07-data-changes.md)

### Example 1

~~~~sql
INSERT INTO learning.Learners (DisplayName, Email)
OUTPUT inserted.LearnerId, inserted.DisplayName
VALUES (N'Mina Rao', N'mina@example.com');
GO
~~~~

### Example 2

~~~~sql
DECLARE @LearnerId INT = 1;

SELECT LearnerId, DisplayName, IsActive
FROM learning.Learners
WHERE LearnerId = @LearnerId;

UPDATE learning.Learners
SET IsActive = 0
WHERE LearnerId = @LearnerId;
GO
~~~~

### Example 3

~~~~sql
BEGIN TRANSACTION;

SELECT LearnerId, DisplayName
FROM learning.Learners
WHERE LearnerId = 1;

DELETE FROM learning.Learners
WHERE LearnerId = 1;

SELECT @@ROWCOUNT AS DeletedRows;
ROLLBACK TRANSACTION;
GO
~~~~

## Keys, constraints, and relationships

Source: [Open chapter](./08-keys-constraints-and-relationships.md)

### Example 1

~~~~sql
CREATE TABLE learning.Courses (
    CourseId INT IDENTITY(1, 1) NOT NULL,
    CourseCode NVARCHAR(20) NOT NULL,
    Title NVARCHAR(150) NOT NULL,
    CONSTRAINT PK_Courses PRIMARY KEY (CourseId),
    CONSTRAINT UQ_Courses_CourseCode UNIQUE (CourseCode)
);
GO
~~~~

### Example 2

~~~~sql
CREATE TABLE learning.Enrollments (
    LearnerId INT NOT NULL,
    CourseId INT NOT NULL,
    EnrolledAt DATETIME2(0) NOT NULL DEFAULT SYSUTCDATETIME(),
    CONSTRAINT PK_Enrollments PRIMARY KEY (LearnerId, CourseId),
    CONSTRAINT FK_Enrollments_Learners
        FOREIGN KEY (LearnerId) REFERENCES learning.Learners (LearnerId),
    CONSTRAINT FK_Enrollments_Courses
        FOREIGN KEY (CourseId) REFERENCES learning.Courses (CourseId)
);
GO
~~~~

### Example 3

~~~~sql
ALTER TABLE learning.Courses
ADD IsPublished BIT NOT NULL
    CONSTRAINT DF_Courses_IsPublished DEFAULT 0;
GO

ALTER TABLE learning.Courses
ADD CONSTRAINT CK_Courses_Title_NotBlank
CHECK (LEN(LTRIM(RTRIM(Title))) > 0);
GO
~~~~

## Joins and combining result sets

Source: [Open chapter](./09-joins-and-set-operations.md)

### Example 1

~~~~sql
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
~~~~

### Example 2

~~~~sql
SELECT l.LearnerId, l.DisplayName, e.CourseId
FROM learning.Learners AS l
LEFT JOIN learning.Enrollments AS e
    ON e.LearnerId = l.LearnerId;
GO
~~~~

### Example 3

~~~~sql
SELECT l.LearnerId, l.DisplayName
FROM learning.Learners AS l
LEFT JOIN learning.Enrollments AS e
    ON e.LearnerId = l.LearnerId
WHERE e.LearnerId IS NULL;
GO
~~~~

### Example 4

~~~~sql
SELECT DisplayName AS PersonName FROM learning.Learners
UNION ALL
SELECT Title AS PersonName FROM learning.Courses;
GO
~~~~

## Aggregate data with GROUP BY

Source: [Open chapter](./10-aggregation-and-grouping.md)

### Example 1

~~~~sql
SELECT
    COUNT(*) AS LearnerCount,
    AVG(Balance) AS AverageBalance,
    MIN(JoinedAt) AS FirstJoinTime,
    MAX(JoinedAt) AS LatestJoinTime
FROM learning.Learners;
GO
~~~~

### Example 2

~~~~sql
SELECT
    IsActive,
    COUNT(*) AS LearnerCount,
    SUM(Balance) AS TotalBalance
FROM learning.Learners
GROUP BY IsActive;
GO
~~~~

### Example 3

~~~~sql
SELECT
    IsActive,
    COUNT(*) AS LearnerCount
FROM learning.Learners
GROUP BY IsActive
HAVING COUNT(*) >= 5;
GO
~~~~

### Example 4

~~~~sql
SELECT COUNT(DISTINCT CourseId) AS CoursesWithEnrollments
FROM learning.Enrollments;
GO
~~~~

## Subqueries, CTEs, and window functions

Source: [Open chapter](./11-subqueries-ctes-and-window-functions.md)

### Example 1

~~~~sql
SELECT LearnerId, DisplayName, Balance
FROM learning.Learners
WHERE Balance > (
    SELECT AVG(Balance)
    FROM learning.Learners
);
GO
~~~~

### Example 2

~~~~sql
SELECT l.LearnerId, l.DisplayName
FROM learning.Learners AS l
WHERE EXISTS (
    SELECT 1
    FROM learning.Enrollments AS e
    WHERE e.LearnerId = l.LearnerId
);
GO
~~~~

### Example 3

~~~~sql
WITH ActiveLearners AS (
    SELECT LearnerId, DisplayName, JoinedAt
    FROM learning.Learners
    WHERE IsActive = 1
)
SELECT LearnerId, DisplayName, JoinedAt
FROM ActiveLearners
WHERE JoinedAt >= '2026-01-01';
GO
~~~~

### Example 4

~~~~sql
SELECT
    LearnerId,
    DisplayName,
    IsActive,
    JoinedAt,
    ROW_NUMBER() OVER (
        PARTITION BY IsActive
        ORDER BY JoinedAt DESC, LearnerId ASC
    ) AS PositionWithinStatus
FROM learning.Learners;
GO
~~~~

## Views and stored procedures

Source: [Open chapter](./12-views-and-stored-procedures.md)

### Example 1

~~~~sql
CREATE OR ALTER VIEW learning.ActiveLearners
AS
    SELECT LearnerId, DisplayName, Email, JoinedAt
    FROM learning.Learners
    WHERE IsActive = 1;
GO

SELECT LearnerId, DisplayName
FROM learning.ActiveLearners;
GO
~~~~

### Example 2

~~~~sql
CREATE OR ALTER PROCEDURE learning.FindLearners
    @IsActive BIT = NULL
AS
BEGIN
    SET NOCOUNT ON;

    SELECT LearnerId, DisplayName, IsActive
    FROM learning.Learners
    WHERE @IsActive IS NULL OR IsActive = @IsActive;
END;
GO

EXEC learning.FindLearners @IsActive = 1;
GO
~~~~

## Indexes and execution plans

Source: [Open chapter](./13-indexes-and-execution-plans.md)

### Example 1

~~~~sql
CREATE NONCLUSTERED INDEX IX_Learners_IsActive
ON learning.Learners (IsActive)
INCLUDE (DisplayName, JoinedAt);
GO
~~~~

### Example 2

~~~~sql
CREATE NONCLUSTERED INDEX IX_Learners_Active
ON learning.Learners (JoinedAt)
INCLUDE (DisplayName)
WHERE IsActive = 1;
GO
~~~~

### Example 3

~~~~sql
SET STATISTICS IO ON;
SET STATISTICS TIME ON;
GO

SELECT LearnerId, DisplayName, JoinedAt
FROM learning.Learners
WHERE IsActive = 1
ORDER BY JoinedAt DESC;
GO

SET STATISTICS IO OFF;
SET STATISTICS TIME OFF;
GO
~~~~

### Example 4

~~~~sql
-- Often harder to seek on a JoinedAt index
WHERE YEAR(JoinedAt) = 2026

-- Express the same year as a range
WHERE JoinedAt >= '2026-01-01'
  AND JoinedAt <  '2027-01-01'
~~~~

## Transactions, locking, and isolation

Source: [Open chapter](./14-transactions-locking-and-isolation.md)

### Example 1

~~~~sql
SET XACT_ABORT ON;

BEGIN TRY
    BEGIN TRANSACTION;

    UPDATE learning.Learners
    SET Balance = Balance - 25.00
    WHERE LearnerId = 1;

    UPDATE learning.Learners
    SET Balance = Balance + 25.00
    WHERE LearnerId = 2;

    COMMIT TRANSACTION;
END TRY
BEGIN CATCH
    IF XACT_STATE() <> 0
        ROLLBACK TRANSACTION;

    THROW;
END CATCH;
GO
~~~~

## Security, backup, and restore

Source: [Open chapter](./15-security-backup-and-restore.md)

### Example 1

~~~~sql
USE StudyNotes;
GO

CREATE USER app_reader FOR LOGIN app_reader;
GRANT SELECT ON SCHEMA::learning TO app_reader;
GO
~~~~

### Example 2

~~~~sql
BACKUP DATABASE [StudyNotes]
TO DISK = N'D:\SqlBackups\StudyNotes_full.bak'
WITH INIT, CHECKSUM, COMPRESSION;
GO
~~~~

### Example 3

~~~~sql
RESTORE VERIFYONLY
FROM DISK = N'D:\SqlBackups\StudyNotes_full.bak'
WITH CHECKSUM;
GO

RESTORE FILELISTONLY
FROM DISK = N'D:\SqlBackups\StudyNotes_full.bak';
GO
~~~~

## SQL Server with JavaScript

Source: [Open chapter](./16-sql-server-with-javascript.md)

### Example 1

~~~~sh
npm install mssql
~~~~

### Example 2

~~~~powershell
$env:SQL_SERVER = "localhost"
$env:SQL_DATABASE = "StudyNotes"
$env:SQL_USER = "app_reader"
$env:SQL_PASSWORD = "<local-secret>"
~~~~

### Example 3

~~~~javascript
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
~~~~

### Example 4

~~~~javascript
const learnerId = 1

const result = await pool.request()
  .input("LearnerId", sql.Int, learnerId)
  .query(`
    SELECT LearnerId, DisplayName, Email
    FROM learning.Learners
    WHERE LearnerId = @LearnerId
  `)

console.log(result.recordset)
~~~~

### Example 5

~~~~javascript
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
~~~~

### Example 6

~~~~javascript
try {
  const result = await pool.request().query("SELECT DB_NAME() AS DatabaseName")
  console.log(result.recordset[0].DatabaseName)
} finally {
  await pool.close()
}
~~~~

