# 8. Keys, constraints, and relationships

[Back to notes index](../README.md)

| [Previous: Insert, update, and delete data](./07-data-changes.md) | [Notes index](../README.md) | [Next: Joins and combining result sets](./09-joins-and-set-operations.md) |
| --- | --- | --- |

## Identify each row with a key

A primary key identifies a row and cannot contain duplicate or null values. A table has one primary key constraint, which can use one column or a combination of columns. A unique constraint protects another value or combination that must be unique.

~~~sql
CREATE TABLE learning.Courses (
    CourseId INT IDENTITY(1, 1) NOT NULL,
    CourseCode NVARCHAR(20) NOT NULL,
    Title NVARCHAR(150) NOT NULL,
    CONSTRAINT PK_Courses PRIMARY KEY (CourseId),
    CONSTRAINT UQ_Courses_CourseCode UNIQUE (CourseCode)
);
GO
~~~

A surrogate key such as `CourseId` is generated for database relationships. A natural value such as `CourseCode` may still need a unique constraint if the domain requires it.

## Connect tables with foreign keys

A foreign key requires a referenced value to exist in the parent table. A linking table can represent learners enrolled in courses:

~~~sql
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
~~~

The composite primary key prevents the same learner and course pair from being inserted twice. Insert the parent learner and course before inserting their enrollment row.

## Protect values with CHECK and DEFAULT

A `CHECK` constraint limits values to a rule, while a `DEFAULT` provides a value when an insert omits a column:

~~~sql
ALTER TABLE learning.Courses
ADD IsPublished BIT NOT NULL
    CONSTRAINT DF_Courses_IsPublished DEFAULT 0;
GO

ALTER TABLE learning.Courses
ADD CONSTRAINT CK_Courses_Title_NotBlank
CHECK (LEN(LTRIM(RTRIM(Title))) > 0);
GO
~~~

Constraints enforce rules close to the data, including when more than one application writes to the database. Keep the rule understandable and test it with valid and invalid examples.

## Think about delete behavior

A foreign key can block deletion of a parent while child rows still reference it. Cascading delete can be useful when child rows have no meaning without the parent, but it can also remove many records unexpectedly. Choose `ON DELETE` behavior only after considering the lifecycle and retention requirements.

## Check what you learned

1. What does a primary key guarantee?
2. How does a unique constraint differ from a primary key?
3. What is a surrogate key?
4. What does a foreign key check?
5. How does the enrollment table represent a many-to-many relationship?
6. What rule does the composite key on `Enrollments` enforce?
7. How do `CHECK` and `DEFAULT` constraints differ?
8. Why should cascading deletes be chosen carefully?

## References

- [Primary and foreign key constraints](https://learn.microsoft.com/sql/relational-databases/tables/primary-and-foreign-key-constraints)
- [Unique constraints](https://learn.microsoft.com/sql/relational-databases/tables/create-unique-constraints)
- [CHECK constraints](https://learn.microsoft.com/sql/relational-databases/tables/create-check-constraints)
- [Default definitions](https://learn.microsoft.com/sql/relational-databases/tables/specify-default-values-for-columns)
