[[_TOC_]]

## SQL Database Programming
#### Introduction to Databases
- What is a Database?  
> A database is a set of related information.

- What is a SQL?
> Structured Query Language (SQL) is a special-purpose programming language designed for managing data held in a Relational Database Management System (RDBMS). SQL-like languages can also be used in Relational Data Stream Management Systems (RDSMS), or in "not-only SQL" (NoSQL) databases. 

- Data Definition Language (*DDL*): to *create* and *modify* the structure of the database;
- Data Manipulation Language (*DML*): to perform *Read*, *Insert*, *Update* and *Delete* operations on the data of the database;
- Data Control Language (*DCL*): to control the access of the data stored in the database.
- Transaction Control Language (*TCL*)

![SQL MindMap.png](/Resources/SQLMindMap.png)


#### Creating, Populating and Modifying Tables
- Table Creation
``` SQL
-- Definition of table columns
CREATE TABLE [dbo].[rContact]
(
  [rContactID] [int] NOT NULL IDENTITY(1, 1),
  [Code] [nvarchar] (100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
  [Name] [nvarchar] (200) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
  [UID] [nvarchar] (100) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
  [Type] [smallint] NOT NULL,
  [rContactGroupID] [int] NULL
) ON [PRIMARY]
GO

-- Adding the PRIMARY KEY 
ALTER TABLE [dbo].[rContact] ADD CONSTRAINT [PK_rContact_rContactID] PRIMARY KEY CLUSTERED ([rContactID]) ON [PRIMARY]
GO

-- Creation of INDEX
CREATE NONCLUSTERED INDEX [NCI_rContact_rContactID] ON [dbo].[rContact] ([rContactID]) ON [PRIMARY]
GO

-- Adding UNIQUE CONSTRAINT on Column Code
ALTER TABLE [dbo].[rContact] ADD CONSTRAINT [UC_rContact_Code] UNIQUE NONCLUSTERED ([Code]) ON [PRIMARY]
GO
/*
  The mask of the names for Primary key, Indexes and Constraints must be "Type_TableName_AffectedCollumns"
  Most used: 
    PRIMARY KEY -> PK
    FOREIGN KEY -> FK
    NONCLUSTERED INDEX -> NCI
    UNIQUE CONSTRAINT -> UC
*/  
```
- Inserting Data
``` SQL
  /* Syntax:
    INSERT INTO table_name (column1, column2, column3, ...)
    VALUES (value1, value2, value3, ...);
  */

  -- Example
    INSERT INTO dbo.rContact ([Code], [Name], [UID], [Type], [rContactGroupID])
    VALUES ( N'0001', N'Test Contact', N'000000000001', 1, NULL )
```
- Updating Data
``` SQL
  /*
    Syntax:
    UPDATE table_name
    SET column1 = value1, column2 = value2, ...
    WHERE condition;
  */

  -- Example
    UPDATE dbo.rContact SET Code = '00002', Name = 'Test Update' WHERE rContactID = 1
```

- Deleting Data
``` SQL
  /*
    Syntax:
    DELETE FROM table_name
    WHERE condition;
  */

  -- Example
    DELETE FROM dbo.rContact WHERE rContactID = 1
```

In SQL also we can use two more types of tables temporary tables and variables as tables
- A temporary SQL table, also known as a temp table, is a table that is created and used within the context of a specific session or transaction in a database management system. It is designed to store temporary data that is needed for a short duration and does not require a permanent storage solution.

- The table variable is a special type of the local variable that helps to store data temporarily, similar to the temp table in SQL Server. In fact, the table variable provides all the properties of the local variable, but the local variables have some limitations, unlike temp or regular tables.

Differences between this two types of tables and primary tables are only in syntax for creation

- Syntax and example for temporary tables
``` SQL
-- Syntax
    IF OBJECT_ID('tempdb..#TableName') IS NULL
    CREATE TABLE #TableName ( ColumnList )

-- Example
    IF OBJECT_ID('tempdb..#tempTable ') IS NULL
   CREATE TABLE #tempTable ( Column1 INT, Column2 NVARCHAR(MAX), Column3 DATE)
```

The **IF** statement we use as prevention if table already exists to not be created again

- Syntax and example for table as variables
``` SQL
-- Syntax
    DECLARE @TableName AS TABLE ( ColumnList )

-- Example
    DECLARE @tempTable AS TABLE ( Column1 INT, Column2 NVARCHAR(MAX), Column3 DATE)
```

#### Query
We use queries in SQL to handle databases. A query is more like a question or a request in simple terms. Suppose you have a query -- Please provide me with the Employee ID of all the employees working in the Accounts department. Or a query like -- how many seats are booked for the show? Hence, we are querying or requesting for some information.
|Clause name|Purpose|
|-|-|
|Select|Determines which columns to include in the query’s result set|
|From|Identifies the tables from which to draw data and how the tables should be joined| 
|Where|Filters out unwanted data| 
|Group by|Used to group rows together by common column values| 
|Having|Filters out unwanted groups|  
|Order by|Sorts the rows of the final result set by one or more columns|  
 
#### Filtering
  SQL filters are text strings that you use to specify a subset of the data items in an internal or SQL database data type. 
  For SQL database and internal data types, the filter is an SQL WHERE clause that provides a set of comparisons that must be true in order for a data item to be returned.
  The SQL filter syntax supports the following comparative operators:  

|Operator|Description|
|-|-|
|=|	Equal|	
|>|	Greater than|	
|<|	Less than|	
|>=|	Greater than or equal|	
|<=|	Less than or equal|	
|<>|	Not equal. Note: In some versions of SQL this operator may be written as !=|	
|BETWEEN|	Between a certain range|	
|LIKE|	Search for a pattern|	
|IN|	To specify multiple possible values for a column|
|NOT BETWEEN| Records who are not	between a certain range|	
|NOT LIKE|	Search different records for a pattern|	
|NOT IN|	To specify multiple not possible values for a column|

#### Removing Duplicates
The SELECT DISTINCT statement is used to return only distinct (different) values.

``` SQL
/*
  Syntax:
  SELECT DISTINCT column1, column2, column3, ... FROM table
*/
-- Example
  SELECT DISTINCT Name, UID FROM dbo.rContact;
```

#### SQL Select Top
The SELECT TOP clause is used to specify the number of records to return.
The SELECT TOP clause is useful on large tables with thousands of records. Returning a large number of records can impact performance.

Note: Not all database systems support the SELECT TOP clause. MySQL supports the LIMIT clause to select a limited number of records, while Oracle uses FETCH FIRST n ROWS ONLY and ROWNUM.

``` SQL
/*
  Syntax:
    SELECT TOP number|percent column_name(s)
    FROM table_name
    WHERE condition;
*/

-- Example
  SELECT TOP 20 * FROM dbo.rPlace
```

Usually when we use TOP after WHERE part we use also and ORDER BY to specify which rows we need the first N or last N rows
``` SQL
/*
  Syntax:
    SELECT TOP number|percent column_name(s)
    FROM table_name
    WHERE condition;
    ORDER BY column_name(s) ASC/DESC

    ASC - we use when we need the first N rows
    DESC - we use when we need the last N rows
*/

-- Example
  SELECT TOP 20 Code, Name, PostalCode 
  FROM dbo.rPlace
  WHERE rPlaceID > 10
  ORDER BY Code ASC

  SELECT TOP 20 Code, Name, PostalCode 
  FROM dbo.rPlace
  WHERE rPlaceID > 10
  ORDER BY Code DESC
```

#### Grouping, Aggregates and Aliasess
- Grouping Concepts
  The GROUP BY statement groups rows that have the same values into summary rows, like "find the number of customers in each country".
  The GROUP BY statement is often used with aggregate functions (COUNT(), MAX(), MIN(), SUM(), AVG()) to group the result-set by one or more columns.

``` SQL
  /*
    Syntax:
      SELECT column_name(s)
      FROM table_name
      WHERE condition
      GROUP BY column_name(s)
  */
```

- Aggregate Functions
    - Count()
    > Returns the number of values in a set
    - Max()
    > Returns the maximum value within a set
   - Min()
    > Returns the minimum value within a set
   - Avg()
    > Returns the average value across a set
   - Sum()
    > Returns the sum of the values across a set
   
   Examples:
``` SQL
  -- Example COUNT()
    SELECT COUNT(loLoanID), rOfficeID
    FROM dbo.loLoan
    GROUP BY rOfficeID;

  -- Example MAX()
    SELECT MAX(loLoanID), rOfficeID
    FROM dbo.loLoan
    GROUP BY rOfficeID;

  -- Example MIN()
    SELECT MIN(loLoanID), rOfficeID
    FROM dbo.loLoan
    GROUP BY rOfficeID;

  -- Example AVG()
    SELECT AVG(LoanAmountCurrent), rOfficeID
    FROM dbo.loLoan
    GROUP BY rOfficeID;

  -- Example SUM()
    SELECT SUM(LoanAmountCurrent), rOfficeID
    FROM dbo.loLoan
    GROUP BY rOfficeID;
```
- Aliasess
  SQL aliases are used to give a table, or a column in a table, a temporary name. Aliases are often used to make column names more readable. 
  An alias only exists for the duration of that query. An alias is created with the AS keyword.

Examples:
``` SQL
  -- Example COUNT()
    SELECT COUNT(loLoanID) AS NO_loLoanIDs, rOfficeID
    FROM dbo.loLoan
    GROUP BY rOfficeID;

  -- Example MAX()
    SELECT MAX(loLoanID) MAX_loLoanID, rOfficeID
    FROM dbo.loLoan
    GROUP BY rOfficeID;

  -- Example MIN()
    SELECT MIN(loLoanID) MIN_loLoanID, rOfficeID
    FROM dbo.loLoan
    GROUP BY rOfficeID;

  -- Example AVG()
    SELECT AVG(LoanAmountCurrent) AVG_LoanAmountCurrent, rOfficeID
    FROM dbo.loLoan
    GROUP BY rOfficeID;

  -- Example SUM()
    SELECT SUM(LoanAmountCurrent) Total_LoanAmountCurrent, rOfficeID
    FROM dbo.loLoan
    GROUP BY rOfficeID;
```

#### Group Filter Conditions
The HAVING clause was added to SQL because the WHERE keyword cannot be used with aggregate functions.

``` SQL
  /*
    Syntax:
      SELECT column_name(s)
      FROM table_name
      WHERE condition
      GROUP BY column_name(s)
      HAVING condition;
  */

  -- Example
    SELECT SUM(LoanAmountCurrent) Total_LoanAmountCurrent, rOfficeID
    FROM dbo.loLoan
    GROUP BY rOfficeID
    HAVING SUM(LoanAmountCurrent) >= 10000;
```
#### Conditional Logic
Conditional logic is simply the ability to take one of several paths during program execution. 
The CASE expression goes through conditions and returns a value when the first condition is met (like an if-then-else statement). 
So, once a condition is true, it will stop reading and return the result. If no conditions are true, it returns the value in the ELSE clause.
If there is no ELSE part and no conditions are true, it returns NULL.

``` SQL
  /*
    Syntax:
    SELECT
      CASE
        WHEN condition1 THEN result1
        WHEN condition2 THEN result2
        WHEN conditionN THEN resultN
        ELSE result
      END AS Result
    FROM table_name
  */

  -- Example
    SELECT 
      loLoanID, 
      AgreementNumber,
      CASE
        WHEN LoanAmountCurrent > 30 AND LoanAmountCurrent <= 100000 THEN 'Medium Classification'
        WHEN LoanAmountCurrent > 100000 THEN 'High Classification'
        ELSE 'Low Classification'
      END AS ClassificationByLoanAmountCurrent
  FROM dbo.loLoan;
```

The following SQL will order the places by PostalCode. However, if PostalCode is NULL, then order by Code:
``` SQL
SELECT Code, Name, PostalCode
FROM dbo.rPlace
ORDER BY
(
  CASE
    WHEN PostalCode IS NULL THEN Code
    ELSE PostalCode
  END
);
```
#### Wildcard Characters
  A wildcard character is used to substitute one or more characters in a string.
  Wildcard characters are used with the LIKE operator. The LIKE operator is used in a WHERE clause to search for a specified pattern in a column.
  
  |Symbol|Description|
  |-|-|
  |%|	Represents zero or more characters|
  |_|	Represents a single character|

``` SQL
  -- Example
    SELECT * 
    FROM dbo.rCountry
    WHERE Name LIKE 'A%';
```
#### Subqueries
A subquery is a query contained within another SQL statement. A subquery is always enclosed within parentheses, and it is usually executed before the containing statement.  

Like any query, a subquery returns a result set that may consist of:
- A single row with a single column
- Multiple rows with a single column
- Multiple rows and columns

``` SQL
  -- Example
    SELECT accAccountID, AccountNumber, rContactID, rOfficerID
    FROM dbo.accAccount
    WHERE rOfficeID IN (
      SELECT rOfficeID
      FROM dbo.rOffice 
      WHERE Name LIKE 'Ants%'
    );
```

#### Table relationships 
The term relation is sometimes used to refer to a table in a relational database. However, it is more often used to describe the relationships that exist between the tables in a relational database.
A relationship between two database tables presupposes that one of them has a foreign key that references the primary key of another table.
Database entity—strictly speaking — is a person, place, thing, object, or any item about which data is stored in the database. However, the term is usually used to refer to the database table as tables are, in fact, the physical implementation of entities.
An entity-relationship diagram, also known as ERD, ER diagram, or ER model, comprises a graphical representation of how entities relate to each other within a database.
ER models are widely used in database design as they are fairly abstract and are easy to view and analyze.

Types of relationships in a database

|Relation Type| Description|
|-|-|
|One To Menu (1:M)|A one-to-many relationship occurs when one record in table 1 is related to one or more records in table 2. However, one record in table 2 cannot be related to more than one record in table 1|
|Menu To Menu (M:M)|A one-to-one relationship in a database occurs when each row in table 1 has only one related row in table 2|
|One To One (1:M)|A many-to-many relationship occurs when multiple records in one table are related to multiple records in another table.|

#### Querying Multiple Tables 
  - Inner Joins
  - Outer Join 
    - LEFT (OUTER) JOIN - return all rows from the left table and those that meet the condition from the right table.
    - RIGHT (OUTER) JOIN - return all rows from the right table and those that meet the condition from the left table.
    - FULL (OUTER) JOIN - return all rows with a match in either table.
  - Cross Join    

  ![XXX](/SQL_Database_Programming/Resources/SQL_JOINS.jpeg)   

#### Working with Sets
Set operations in SQL is a type of operations which allow the results of multiple queries to be combined into a single result set.
Set operators in SQL include UNION, INTERSECT, and EXCEPT, which mathematically correspond to the concepts of union, intersection and set difference.

- UNION - In SQL the UNION clause combines the results of two SQL queries into a single table of all matching rows. 
The two queries must result in the same number of columns and compatible data types in order to unite. Any duplicate records are automatically removed unless UNION ALL is used.
Note that UNION ALL does not guarantee the order of rows. Rows from the second operand may appear before, after, or mixed with rows from the first operand. In situations where a specific order is desired, ORDER BY must be used.
 
 ``` SQL
  SELECT rContactID, FirstName FROM dbo.rPerson
  UNION
  SELECT rContactID, ShortName FROM dbo.rCompany;
 ```
- INTERSECT - The SQL INTERSECT operator takes the results of two queries and returns only rows that appear in both result sets. For purposes of duplicate removal the INTERSECT operator does not distinguish between NULLs. 
The INTERSECT operator removes duplicate rows from the final result set. The INTERSECT ALL operator does not remove duplicate rows from the final result set, but if a row appears X times in the first query and Y times in the second, it will appear 
MIN(X,Y) times in the result set.

``` SQL
  SELECT *
  FROM   dbo.loApplication
  WHERE  RequestedAmount BETWEEN 1 AND 100
  INTERSECT
  SELECT *
  FROM   dbo.loApplication
  WHERE  RequestedAmount BETWEEN 50 AND 200;
 ```
- EXCEPT - The SQL EXCEPT operator takes the distinct rows of one query and returns the rows that do not appear in a second result set. For purposes of row elimination and duplicate removal, the EXCEPT operator does not distinguish between NULLs. 
The EXCEPT ALL operator does not remove duplicates, but if a row appears X times in the first query and Y times in the second, it will appear 
MAX(X-Y) times in the result set.

 ``` SQL
  SELECT *
  FROM   dbo.loApplication
  WHERE  RequestedAmount BETWEEN 1 AND 100
  EXCEPT
  SELECT *
  FROM   dbo.loApplication
  WHERE  RequestedAmount BETWEEN 50 AND 75;
 ```

#### Sequence
SQL sequences specifies the properties of a sequence object while creating it. An object bound to a user-defined schema called a sequence produces a series of numerical values in accordance with the specification used to create it. The series of numerical values can be configured to restart (cycle) when it runs out and is generated in either ascending or descending order at a predetermined interval. Contrary to identity columns, sequences are not linked to particular tables. Applications use a sequence object to access the next value in the sequence. The application has control over how sequences and tables interact. A sequence object can be referred to by user applications, and the values can be coordinated across various rows and tables.

Let’s suppose that sequence is a set of integers 1, 2, 3, … that are generated and supported by some database systems to produce unique values on demands.

A sequence is a user-defined schema-bound object that generates a series of numeric values.
Sequences are frequently used in many databases because many applications require each row in a table to contain a unique value and sequences provide an easy way to generate them.
The sequence of numeric values is generated in an ascending or descending order at defined intervals and can be configured to restart when it exceeds max_value.

Different Features of Sequences
1. A sequence is a database object that generates and produces integer values in sequential order.
2. It automatically generates the primary key and unique key values.
3. It may be in ascending or descending order.
4. It can be used for multiple tables.
5. Sequence numbers are stored and generated independently of tables.
6. It saves time by reducing application code.
7. It is used to generate unique integers.
8. It is used to create an auto number field.
9. Useful when you need to create a unique number to act as a primary key.
10. Oracle provides an object called a Sequence that can generate numeric values. The value generated can have maximum of 38 digits
11. Provide intervals between numbers.

``` SQL
/*
  Syntax
  CREATE SEQUENCE sequence_name
  START WITH initial_value
  INCREMENT BY increment_value
  MINVALUE minimum value
  MAXVALUE maximum value
  CYCLE|NOCYCLE ;
*/

-- Example
  CREATE SEQUENCE sequence_1
  start with 1
  increment by 1
  minvalue 0
  maxvalue 100
  cycle;
```

The above query will create a sequence named sequence_1. The sequence will start from 1 and will be incremented by 1 having maximum value of 100. 
The sequence will repeat itself from the start value after exceeding 100.

Example To Use Sequence:
``` SQL
  CREATE TABLE students ( ID number(10), NAME char(20) 
  
  INSERT into students 
  VALUES (sequence_1.nextval,'Shubham');

  INSERT into students 
  VALUES (sequence_1.nextval,'Aman');
```
In above example we create table students and inserting new records where sequence_1.nextval will insert id’s in the id column in a sequence as defined in sequence_1

#### SQL Cursor
- Cursor is a database object to retrieve data from a result set one row at a time, instead of the T-SQL commands that operate on all the rows in the result set at one time. We use a cursor when we need to update records in a database table in singleton fashion means row by row.

Life Cycle of Cursor
1.	Declare Cursor
A cursor is declared by defining the SQL statement that returns a result set.
2.	Open
A Cursor is opened and populated by executing the SQL statement defined by the cursor.
3.	Fetch
When the cursor is opened, rows can be fetched from the cursor one by one or in a block to do data manipulation.
4.	Close
After data manipulation, we should close the cursor explicitly.
5.	Deallocate
Finally, we need to delete the cursor definition and released all the system resources associated with the cursor.

Example
``` SQL
/*
  Syntax
  DECLARE @Varibale_1 INT, @Varibale_2 INT, ...
  DECLARE tsk_Cursor CURSOR FAST_FORWARD READ_ONLY FOR 
	  SELECT Column_1, Column_2, ... FROM table_name
  OPEN tsk_Cursor
  FETCH NEXT FROM tsk_Cursor INTO @Varibale_1, @Varibale_2, ...
  WHILE @@FETCH_STATUS = 0
  BEGIN
        Logic statements
  FETCH NEXT FROM tsk_Cursor INTO @Varibale_1, @Varibale_2, ...
  END

  CLOSE tsk_Cursor
  DEALLOCATE tsk_Cursor

  Note: All variables used in cursor must be from the same type as the returnung columns from table_name
*/

DECLARE @ID INT
DECLARE tsk_Cursor CURSOR FAST_FORWARD READ_ONLY FOR 
	SELECT rContactID FROM dbo.rContact
OPEN tsk_Cursor
FETCH NEXT FROM tsk_Cursor INTO @ID
WHILE @@FETCH_STATUS = 0
BEGIN
      SELECT @ID
FETCH NEXT FROM tsk_Cursor INTO @ID
END

CLOSE tsk_Cursor
DEALLOCATE tsk_Cursor
```
#### SQL While loop
SQL WHILE loop provides us with the advantage to execute the SQL statement(s) repeatedly until the specified condition result turn out to be false. 
 
``` SQL
/*
  Syntax
  WHILE condition
  BEGIN
     		{...statements...}
  END
*/

-- Example
  DECLARE @Counter INT = 1
  WHILE ( @Counter <= 10)
  BEGIN
    PRINT 'The counter value is = ' + CAST(@Counter AS NVARCHAR(MAX))
    SET @Counter = @Counter + 1
  END
```


#### Stored procedures
> Stored Procedures are SQL statements stored in the database that can be executed or called in queries. Using a stored procedure allows encapsulation of complicated or frequently used logic, and improves query performance by utilizing cached query plans. 
Output of stored procedure can be equal as standard query can return or it can return multiple values through output parameters.


``` SQL
/*
  Syntax
    CREATE PROCEDURE ProcedureName Paramaeter_1, Parameter_2, ...
    AS 
      statement
    GO
*/

-- Example
CREATE PROCEDURE [dbo].[me_batch_CreateUpdateMembership_sp] @rContactID INT, @rAgreementID INT = NULL, @meMembershipTypeID INT = NULL, @cmdCommit BIT = 1 OUTPUT, @Message NVARCHAR(MAX) = NULL OUT
AS
/**********************************************************************************************************
* Description: 
	System procedure for createing or update membership for given ContactID and loLoanID
	                      
* Input parameters:
	@rContactID - ID for Contact that we like to create or update the membership
	@loLoanID	- ID of the Loan who have some changes
  @meMembershipTypeID - ID of the membership that we like to create or update

* Outupt parameters:
	@cmdCommit - Flag if procedure is executed successfully
  @Message - Parameter in which we store the information why procedure is not executed successfully

* Created: 

* Modified:
	
* Cal example

* Note
	Membership Statuses
		[New = 1]
		[Active = 2]
		[Inactive = 3]
		[TemporaryInactive = 4]

********************************************************************************************************/
	SET @cmdCommit = ISNULL(@cmdCommit, 1) 
	DECLARE @meMembershipID INT
	
	SELECT @meMembershipID = dbo.me_return_CheckValidityForMembership_f(@rContactID) -- Function which return if Contact can be member or not
	
	IF @meMembershipID >= 0 BEGIN -- If Contact can be Member continue 
		IF @meMembershipID = 0  BEGIN -- Contact can be Member but he doesn't have Active Membership we need to generate it
			EXEC dbo.me_batch_GenerateMembership_sp @rContactID = @rContactID, @rAgreementID = @rAgreementID, @meMembershipTypeID = @meMembershipTypeID, 
													@meMembershipID = @meMembershipID OUTPUT, @cmdCommit = @cmdCommit OUTPUT, @Message = @Message OUTPUT
		END 
		
		IF @cmdCommit = 1 BEGIN -- Contact has already active Membership
			EXEC dbo.me_batch_UpdateMembership_sp @rContactID = @rContactID, @rAgreementID = @rAgreementID
		END
	END
	
GO

-- Call Example
DECLARE @cmdCommit BIT = 1, @Message NVARCHAR(MAX)
EXEC dbo.[dbo].[me_batch_CreateUpdateMembership_sp] @rContactID = 1, @rAgreementID = 123, @meMembershipTypeID = 1, @cmdCommit = @cmdCommit OUTPUT, @Message = @Message OUTPUT
SELECT @cmdCommit, @Message
```

If we want to make some changes in procedure logic or to add additional input or output parameter we use the key word ALTER instead of CREATE
``` SQL
ALTER PROCEDURE [dbo].[me_batch_CreateUpdateMembership_sp] @rContactID INT, @rAgreementID INT = NULL, @meMembershipTypeID INT = NULL, @cmdCommit BIT = 1 OUTPUT, @Message NVARCHAR(MAX) = NULL OUT
AS
/**********************************************************************************************************
* Description: 
	System procedure for createing or update membership for given ContactID and loLoanID
	                      
* Input parameters:
	@rContactID - ID for Contact that we like to create or update the membership
	@loLoanID	- ID of the Loan who have some changes
  @meMembershipTypeID - ID of the membership that we like to create or update

* Outupt parameters:
	@cmdCommit - Flag if procedure is executed successfully
  @Message - Parameter in which we store the information why procedure is not executed successfully

* Created: 

* Modified:

* Cal example

* Note
	Membership Statuses
		[New = 1]
		[Active = 2]
		[Inactive = 3]
		[TemporaryInactive = 4]

********************************************************************************************************/
	SET @cmdCommit = ISNULL(@cmdCommit, 1) 
	DECLARE @meMembershipID INT
	
	SELECT @meMembershipID = dbo.me_return_CheckValidityForMembership_f(@rContactID) -- Function which return if Contact can be member or not
	
	IF @meMembershipID >= 0 BEGIN -- If Contact can be Member continue 
    PRINT '@meMembershipID >= 0'
		IF @meMembershipID = 0  BEGIN -- Contact can be Member but he doesn't have Active Membership we need to generate it
			PRINT '@meMembershipID = 0'
      EXEC dbo.me_batch_GenerateMembership_sp @rContactID = @rContactID, @rAgreementID = @rAgreementID, @meMembershipTypeID = @meMembershipTypeID, 
													@meMembershipID = @meMembershipID OUTPUT, @cmdCommit = @cmdCommit OUTPUT, @Message = @Message OUTPUT
		END 
		
		IF @cmdCommit = 1 BEGIN -- Contact has already active Membership
			PRINT 'Calling procedure dbo.me_batch_UpdateMembership_sp'
      EXEC dbo.me_batch_UpdateMembership_sp @rContactID = @rContactID, @rAgreementID = @rAgreementID
		END
	END
	
GO
```

Syntax for deleting store procedure
``` SQL 
  DROP PROCEDURE procedure_name
```
Example
``` SQL
DROP PROCEDURE dbo.me_batch_CreateUpdateMembership_sp
```
#### SQL Functions
###### SQL Server Functions

1. **String Functions**

|Function|Description|
|-|-|
|ASCII|Returns the ASCII value for the specific character|
|CHAR|Returns the character based on the ASCII code|
|CHARINDEX|Returns the position of a substring in a string|
|CONCAT|Adds two or more strings together|
|Concat with +|Adds two or more strings together|
|CONCAT_WS|Adds two or more strings together with a separator|
|DATALENGTH|Returns the number of bytes used to represent an expression|
|DIFFERENCE|Compares two SOUNDEX values, and returns an integer value|
|FORMAT|Formats a value with the specified format|
|LEFT|Extracts a number of characters from a string (starting from left)|
|LEN|Returns the length of a string|
|LOWER|Converts a string to lower-case|
|LTRIM|Removes leading spaces from a string|
|NCHAR|Returns the Unicode character based on the number code|
|PATINDEX|Returns the position of a pattern in a string|
|QUOTENAME|Returns a Unicode string with delimiters added to make the string a valid SQL Server delimited identifier|
|REPLACE|Replaces all occurrences of a substring within a string, with a new substring|
|REPLICATE|Repeats a string a specified number of times|
|REVERSE|Reverses a string and returns the result|
|RIGHT|Extracts a number of characters from a string (starting from right)|
|RTRIM|Removes trailing spaces from a string|
|SOUNDEX|Returns a four-character code to evaluate the similarity of two strings|
|SPACE|Returns a string of the specified number of space characters|
|STR|Returns a number as string|
|STUFF|Deletes a part of a string and then inserts another part into the string, starting at a specified position|
|SUBSTRING|Extracts some characters from a string|
|TRANSLATE|Returns the string from the first argument after the characters specified in the second argument are translated into the characters specified in the third argument.|
|TRIM|Removes leading and trailing spaces (or other specified characters) from a string|
|UNICODE|Returns the Unicode value for the first character of the input expression|
|UPPER|Converts a string to upper-case|

2. **Math/Numeric Functions**

|Function|Description|
|-|-|
|ABS|Returns the absolute value of a number|
|ACOS|eturns the arc cosine of a number|
|ASIN|eturns the arc sine of a number|
|ATAN|eturns the arc tangent of a number|
|ATN2|Returns the arc tangent of two numbers|
|AVG|Returns the average value of an expression|
|CEILING|Returns the smallest integer value that is >= a number|
|COUNT|Returns the number of records returned by a select query|
|COS|Returns the cosine of a number|
|COT|Returns the cotangent of a number|
|DEGREES|Converts a value in radians to degrees|
|EXP|Returns e raised to the power of a specified number|
|FLOOR|Returns the largest integer value that is <= to a number|
|LOG|Returns the natural logarithm of a number, or the logarithm of a number to a specified base|
|LOG10|Returns the natural logarithm of a number to base 10|
|MAX|Returns the maximum value in a set of values|
|MIN|Returns the minimum value in a set of values|
|PI|Returns the value of PI|
|POWER|Returns the value of a number raised to the power of another number|
|RADIANS|Converts a degree value into radians|
|RAND|Returns a random number|
|ROUND|Rounds a number to a specified number of decimal places|
|SIGN|Returns the sign of a number|
|SIN|Returns the sine of a number|
|SQRT|Returns the square root of a number|
|SQUARE|Returns the square of a number|
|SUM|Calculates the sum of a set of values|
|TAN|Returns the tangent of a number|

3. **Date Functions**

|Function|Description|
|-|-|
|CURRENT_TIMESTAMP|Returns the current date and time|
|DATEADD|Adds a time/date interval to a date and then returns the date|
|DATEDIFF|Returns the difference between two dates|
|DATEFROMPARTS|Returns a date from the specified parts (year, month, and day values)|
|DATENAME|Returns a specified part of a date (as string)|
|DATEPART|Returns a specified part of a date (as integer)|
|DAY|Returns the day of the month for a specified date|
|GETDATE|Returns the current database system date and time|
|GETUTCDATE|Returns the current database system UTC date and time|
|ISDATE|Checks an expression and returns 1 if it is a valid date, otherwise 0|
|MONTH|Returns the month part for a specified date (a number from 1 to 12)|
|SYSDATETIME|Returns the date and time of the SQL Server|
|YEAR|Returns the year part for a specified date|

4. **Advanced Functions**

|Function|Description|
|-|-|
|CAST|Converts a value (of any type) into a specified datatype|
|COALESCE|Returns the first non-null value in a list|
|CONVERT|Converts a value (of any type) into a specified datatype|
|CURRENT_USER|Returns the name of the current user in the SQL Server database|
|IIF|Returns a value if a condition is TRUE, or another value if a condition is FALSE|
|ISNULL|Return a specified value if the expression is NULL, otherwise return the expression|
|ISNUMERIC|Tests whether an expression is numeric|
|NULLIF|Returns NULL if two expressions are equal|
|SESSION_USER|Returns the name of the current user in the SQL Server database|
|SESSIONPROPERTY|Returns the session settings for a specified option|
|SYSTEM_USER|Returns the login name for the current user|
|USER_NAME|Returns the database user name based on the specified id|


###### User-Defned Functions
The main idea behind objects is to have them stored in the database and avoid writing the same code over and over again. Also, you can control what is the input and define the structure/type of output. 
And last, but not least, you can define permissions to decide who’ll be able to use them and in what way he’ll be able to do it. 

Syntaxes for Creation, Updating and Deleting User-Defined Functions are
``` SQL
CREATE FUNCTION [database_name.]function_name (parameters)
RETURNS data_type AS
BEGIN
    SQL statements
    RETURN value
END;
    
ALTER FUNCTION [database_name.]function_name (parameters)
RETURNS data_type AS
BEGIN
    SQL statements
    RETURN value
END;
    
DROP FUNCTION [database_name.]function_name;
```
There is two types of User-Defined Functions

- Scalar functions - This type of function can return only one result dependent of type
``` sql
CREATE FUNCTION [dbo].[acc_ReturnAmount_f] (@accAccountID INT, @rCurrencyID INT, @Ondate date = null, @Type int = null)
RETURNS decimal(18,2)
/**********************************************************************************************************
* Description: 
	Procedure who returns specific balance for given account, currency, date and type of balance
                        
* Input parametars:
	@accAccountID - ID of Account for what we like to get Balance
	@rCurrencyID - ID of Currency in which we like to get Balance
  @Ondate - Date on which we like to get Balance
	@Type	- 1 Principal
          2 Interest

* Returns:
	Balance for given parametars

* Created: 

* Modification:

* Exaple:
	  SELECT dbo.acc_ReturnAmount_f(1, 1, '2022-05-25', 1)

* Note:

**********************************************************************************************************/
AS
BEGIN
  DECLARE @Balance DECIMAL(18,2)
  SELECT @Balance = SUM(AmountCRY*DebitCredit) 
  FROM dbo.accAccountTransaction 
  WHERE accAccountID = @accAccountID AND rCurrencyID = @rCurrencyID AND accAccountTransactionType = @Type AND PostingDate <= @Date

  RETURN @Balance
END

GO
```

- Table-valued functions - In this type of function return result must be a table
``` sql
CREATE FUNCTION [dbo].[acc_ReturnAmount_tbl_f] (@accAccountID INT, @rCurrencyID INT, @Ondate date = null, @Type int = null)
RETURNS @retTbl TABLE (rCurrencyID INT, Balance DECIMAL(18,2))
/**********************************************************************************************************
* Description: 
	Procedure who returns specific balance for given account, currency, date and type of balance
                        
* Input parametars:
	@accAccountID - ID of Account for what we like to get Balance
	@rCurrencyID - ID of Currency in which we like to get Balance
  @Ondate - Date on which we like to get Balance
	@Type	- 1 Principal
          2 Interest

* Returns:
	Balance for given parametars

* Created: 

* Modification:

* Exaple:
	  SELECT dbo.acc_ReturnAmount_f(1, 1, '2022-05-25', 1)

* Note:

**********************************************************************************************************/
AS
BEGIN
  INSERT INTO @retTbl (rCurrencyID, Balance)
  SELECT rCurrencyID, SUM(AmountCRY*DebitCredit) 
  FROM dbo.accAccountTransaction 
  WHERE accAccountID = @accAccountID AND rCurrencyID = @rCurrencyID AND accAccountTransactionType = @Type AND PostingDate <= @Date
  GROUP BY rCurrencyID

  RETURN
END

GO
```

#### Synonyms
A SYNONYM provides another name for database object, referred to as original object, that may exist on a local or another server. A synonym belongs to schema, name of synonym should be unique. A synonym cannot be original object for an additional synonym and synonym cannot refer to user-defined function.

Syntax
``` SQL
CREATE SYNONYM synonym_name FOR server_name.database_name.schema_name.object_name
GO
```

Exaple
``` SQL
CREATE SYNONYM [mobile].[ExternalUsers] FOR [bagsAPS35_Mobile].[mobile].[ExternalUsers]
GO
```

#### Transactions
> Database management systems allow not only a single user to query and modify data,
but multiple people to do so simultaneously
---
> A transaction is a logical unit of work containing one or more steps, each of which must complete successfully in order for the transaction to commit to the database. If there are errors, then all of the data modifications are erased and the database is rolled back to its initial state at the start of the transaction.
- Locking
Locks are the mechanism the database server uses to control simultaneous use of data resources.

- Lock Granularities
   - Table locks
      - Keep multiple users from modifying data in the same table simultaneously
   - Page locks
     - Keep multiple users from modifying data on the same page (a page is a segment of
memory generally in the range of 2 KB to 16 KB) of a table simultaneously
   - Row locks
     - Keep multiple users from modifying the same row in a table simultaneously

- What Is a Transaction?
> Transaction is a device for grouping together multiple SQL statements such that either all or none of the statements succeed (a property known as atomicity).

``` SQL
CREATE TABLE ValueTable (id INT);  
BEGIN TRANSACTION;  
       INSERT INTO ValueTable VALUES(1);  
       INSERT INTO ValueTable VALUES(2);  
COMMIT / ROLLBACK;  
```

- [Transactions in sql server](https://www.youtube.com/watch?v=shkt9Z5Gz-U&list=PL08903FB7ACA1C2FB&index=59)
- [Transactions in sql server and ACID](https://www.youtube.com/watch?v=VLc4ewu6lUI&list=PL08903FB7ACA1C2FB&index=60)

#### Triggers
A trigger is a stored procedure in a database that automatically invokes whenever a special event in the database occurs. For example, a trigger can be invoked when a row is inserted into a specified table or when specific table columns are updated in simple words a trigger is a collection of SQL statements with particular names that are stored in system memory. It belongs to a specific class of stored procedures that are automatically invoked in response to database server events. Every trigger has a table attached to it.

Because a trigger cannot be called directly, unlike a stored procedure, it is referred to as a special procedure. A trigger is automatically called whenever a data modification event against a table takes place, which is the main distinction between a trigger and a procedure. On the other hand, a stored procedure must be called directly.

The following are the key differences between triggers and stored procedures:

  1. Triggers cannot be manually invoked or executed.
  2. There is no chance that triggers will receive parameters.
  3. A transaction cannot be committed or rolled back inside a trigger.

Syntax
``` SQL
  CREATE TRIGGER [trigger_name] 
  [BEFORE | AFTER]  
  {INSERT | UPDATE | DELETE}  
  ON [table_name]  
  [FOR EACH ROW]  
  [trigger_body] 
```
Explanation of Syntax
1. Create trigger [trigger_name]: Creates or replaces an existing trigger with the trigger_name.
2. [BEFORE | AFTER]: This specifies when the trigger will be executed.
3. {INSERT | UPDATE | DELETE}: This specifies the DML operation.
4. On [table_name]: This specifies the name of the table associated with the trigger.
5. [FOR EACH ROW]: This specifies a row-level trigger, i.e., the trigger will be executed for each affected row.
6. [trigger_body]: This provides the operation to be performed as the trigger is fired

Advantage of Triggers
  The benefits of using triggers in SQL Server include the following:

  1. Database object rules are established by triggers, which cause changes to be undone if they are not met. 
  2. The trigger will examine the data and, if necessary, make changes.
  3. We can enforce data integrity thanks to triggers.
  4. Data is validated using triggers before being inserted or updated.
  5. Triggers assist us in maintaining a records log.
  6. Due to the fact that they do not need to be compiled each time they are run, triggers improve the performance of SQL queries.
  7. The client-side code is reduced by triggers, saving time and labor.
  8. Trigger maintenance is simple.
  
Disadvantage of Triggers
  The drawbacks of using triggers in SQL Server include the following:

  1. Only triggers permit the use of extended validations.
  2. Automatic triggers are used, and the user is unaware of when they are being executed. Consequently, it is difficult to troubleshoot issues that arise in the database layer.
  3. The database server’s overhead may increase as a result of triggers.
  4. In a single CREATE TRIGGER statement, we can specify the same trigger action for multiple user actions, such as INSERT and UPDATE.
  5. Only the current database is available for creating triggers, but they can still make references to objects outside the database.

Example
``` SQL
CREATE TRIGGER [dbo].[tr_AuditLogmeMembership] ON [dbo].[meMembership]
    AFTER INSERT, UPDATE, DELETE
AS

SET NOCOUNT ON
-- excludet users from audit log
  DECLARE @SysUser NVARCHAR(500) = SYSTEM_USER
  IF @SysUser IN ('MKFEKI\aspappproduser','MKFEKI\aspsqlproduser') RETURN; 
   
--Check for empty tables
	IF (NOT EXISTS(SELECT TOP 1 * FROM INSERTED)) AND (NOT EXISTS(SELECT TOP 1 * FROM DELETED))
	RETURN;

DECLARE @DMLType CHAR(1)
IF NOT EXISTS (SELECT TOP 1 * FROM INSERTED)
	SET @DMLType = 'D'
ELSE
BEGIN
	IF EXISTS (SELECT TOP 1 * FROM DELETED)
		SET @DMLType = 'U'
	ELSE 
		SET @DMLType = 'I'
END

DECLARE @TableName VARCHAR(100)
DECLARE @ReferenceTable VARCHAR(100) = ''
DECLARE @ReferenceKeyColumn VARCHAR(100) = 'NULL'


SELECT @TableName = OBJECT_NAME(parent_id)
        FROM      sys.triggers
        WHERE     object_id = @@PROCID

IF @DMLType = 'D'
	SELECT [meMembershipID], [meMembershipTypeID], [rAgreementID], [rOrganizationID], [rContactID], [Code], [Date], [DateTo], [Status], [Note]
	INTO #TempDataD
	FROM DELETED
ELSE
	SELECT [meMembershipID], [meMembershipTypeID], [rAgreementID], [rOrganizationID], [rContactID], [Code], [Date], [DateTo], [Status], [Note]
	INTO #TempDataI
	FROM INSERTED

DECLARE @Text VARCHAR(MAX)

SET @Text = '
DECLARE @xmlContract XML

SET @xmlContract = ( SELECT 
'

DECLARE @ColumnID VARCHAR(100)
SELECT @ColumnID = name FROM sys.columns
WHERE OBJECT_ID = (
	SELECT    parent_id
    FROM      sys.triggers
    WHERE     object_id = @@PROCID)
AND column_id = 1

SET @Text = @Text + '[meMembershipID]  AS ''AuditedData/meMembershipID'', [meMembershipTypeID]  AS ''AuditedData/meMembershipTypeID'', [rAgreementID]  AS ''AuditedData/rAgreementID'', [rOrganizationID]  AS ''AuditedData/rOrganizationID'', [rContactID]  AS ''AuditedData/rContactID'', [Code]  AS ''AuditedData/Code'', [Date]  AS ''AuditedData/Date'', [DateTo]  AS ''AuditedData/DateTo'', [Status]  AS ''AuditedData/Status'', [Note]  AS ''AuditedData/Note'', ' + '
	''' + @DMLType + ''' AS ''Type'',
    ''' + @TableName + ''' AS ''TableName'',

    ' + @ColumnID + ' AS ''PrimaryKey'' ,
    ''' + @ReferenceTable + ''' AS ''ReferenceTable'',
    ' + @ReferenceKeyColumn + ' AS ''ReferenceKey'',

    SYSTEM_USER AS ''SysUsr'' ,
    GETDATE() AS ''SysDt''
FROM #TempData' + CASE WHEN @DMLType = 'D' THEN 'D' ELSE 'I' END
SET @Text = @Text + '
	FOR
	XML PATH (''Row''), ELEMENTS XSINIL,
	ROOT(''Root'')
)

set @xmlcontract = REPLACE(cast(@xmlcontract as nvarchar(max)), ''NULL'', '''')
EXEC dbo.usp_SendAuditData @xmlContract

'

EXEC (@Text)


GO

```
####Import Excel / CSV files
For Implementation purposes we use import files from Excel or CSV, because RTI ( Ready to install ) solution implementation goes through Exhibits. Exhibits represents files that we send to the clients where they fill them with data for migration. First we choose database where we like to import data from file then go **Right Click** on the mouse choose **Tasks** and from sub-menu **Import Data**

![image.png](/.attachments/image-9d0d378f-9864-459d-ae7d-7f1dfc9ca03f.png)

From opened wizard first we go **Next** and after that in field **Data source** we choose **Microsoft Excel** and location from the excel file that we need to import

![image.png](/.attachments/image-526d4320-0adc-4d27-87d3-2d2a4cbbe996.png)

After that we go Next. From newly opened window we choose destination where we like to import the Excel / CSV file

![image.png](/.attachments/image-8b0b2dd9-3ad4-48a8-8929-575170504f92.png)

After choosing destination we choose **Copy data from one or more tables or views**

![image.png](/.attachments/image-cdd90c4a-9bad-4c4c-b5ed-58ab0ddc82d5.png)

After click on the Next button we get new window where we need to choose which sheets of data we like to import and the name of the table where ( if table not exists it will be created automatically )

![image.png](/.attachments/image-1f843ebe-6699-47ae-aaeb-0ca610bd0d74.png)

Then we go **Next** and **Finish** to end with the process.


#### Error Handling
Error handling in SQL Server gives us control over the Transact-SQL code. For example, when things go wrong, we get a chance to do something about it and possibly make it right again. 
SQL Server error handling can be as simple as just logging that something happened, or it could be us trying to fix an error. 
It can even be translating the error in SQL language because we all know how technical SQL Server error messages could get making no sense and hard to understand. Luckily, 
we have a chance to translate those messages into something more meaningful to pass on to the users, developers, etc.

Syntax for TRY_CATCH
``` SQL
  BEGIN TRY  
       --code to try 
  END TRY  
  BEGIN CATCH  
       --code to run if an error occurs
  --is generated in try
  END CATCH
```
Anything between the BEGIN TRY and END TRY is the code that we want to monitor for an error. So, if an error would have happened inside this TRY statement, the control would have immediately get transferred to the CATCH statement and then it would have started executing code line by line.

Now, inside the CATCH statement, we can try to fix the error, report the error or even log the error, so we know when it happened, who did it by logging the username, all the useful stuff. We even have access to some special data only available inside the CATCH statement:
|System Function|Description|
|-|-|
|ERROR_NUMBER|Returns the internal number of the error|
|ERROR_STATE|Returns the information about the source|
|ERROR_SEVERITY|Returns the information about anything from informational errors to errors user of DBA can fix, etc.|
|ERROR_LINE|Returns the line number at which an error happened on|
|ERROR_PROCEDURE|Returns the name of the stored procedure or function|
|ERROR_MESSAGE|Returns the most essential information and that is the message text of the error|

That’s all that is needed when it comes to SQL Server error handling. Everything can be done with a simple TRY and CATCH statement and the only part when it can be tricky is when we’re dealing with transactions. Why? Because if there’s a BEGIN TRANSACTION, it always must end with a COMMIT or ROLLBACK transaction. The problem is if an error occurs after we begin but before we commit or rollback. In this particular case, there is a special function that can be used in the CATCH statement that allows checking whether a transaction is in a committable state or not, which then allows us to make a decision to rollback or to commit it.

Example
``` SQL
BEGIN TRANSACTION
BEGIN TRY
    INSERT INTO dbo.rContact(Code, Name, Type)
    VALUES ('000023', N'Име Презиме', N'A')
    
    COMMIT TRANSACTION
END TRY
BEGIN CATCH
    SELECT
      ERROR_NUMBER() AS ErrorNumber,
      ERROR_STATE() AS ErrorState,
      ERROR_SEVERITY() AS ErrorSeverity,
      ERROR_PROCEDURE() AS ErrorProcedure,
      ERROR_LINE() AS ErrorLine,
      ERROR_MESSAGE() AS ErrorMessage;

    ROLLBACK TRANSACTION
END CATCH
```

#### Views
A view is a tailored table that is formed as a result of a query. It has tables and rows just like any other table. It’s usually a good idea to run queries in SQL as independent views 
because this allows them to be retrieved later to view the query results, rather than computing the same command every time for a particular set of results
A view can be a really complex query(aggregations, joins, subqueries, etc). Just be sure you add column names for everything you select:
A view can filter some rows from the base table or project only some columns from it.

Note: A view always shows up-to-date data! The database engine recreates the view, every time a user queries it.

Syntaxes
``` SQL
-- Creation
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;

-- Updating
ALTER VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;

-- Deleting
DROP VIEW view_name
```

Example
``` SQL
CREATE VIEW [dbo].[cmCommission_w]
AS
SELECT        cmCommissionID AS cmCommission_wID, Code + ' - ' + Name AS Commission_CodeName
FROM            dbo.cmCommission
WHERE        (Status = 1)

GO
```
#### Indexes and Constraints
 - Indexes - Indexes are used to retrieve data from the database more quickly than otherwise. The users cannot see the indexes, they are just used to speed up searches/queries.
- [How do SQL Indexes Work](https://www.youtube.com/watch?v=YuRO9-rOgv4)
- [SQL Indexing and Tuning e-Book](https://use-the-index-luke.com/)
 Syntax
 ``` SQL 
 -- Creation of index where duplicate values are allowed
  CREATE INDEX index_name ON table_name (column1, column2, ...);

-- Creation of index where duplicate values are not allowed
  CREATE UNIQUE  INDEX index_name ON table_name (column1, column2, ...);
```

Examples
``` SQL 
-- Creation of index where duplicate values are allowed
  CREATE INDEX idx_rPerson_Lastname ON sbo.rPersons (LastName); 

-- Creation of index where duplicate values are not allowed
  CREATE UNIQUE INDEX iudx_rPerson_FirsName_LastName ON sbo.rPersons (FirstName,LastName); 
```

- Constraints - Constraints can be specified when the table is created with the CREATE TABLE statement, or after the table is created with the ALTER TABLE statement
Constraints are used to limit the type of data that can go into a table. This ensures the accuracy and reliability of the data in the table. If there is any violation between the constraint and the data action, the action is aborted.

The following constraints are commonly used in SQL:

1. NOT NULL - Ensures that a column cannot have a NULL value
2. UNIQUE - Ensures that all values in a column are different
3. PRIMARY KEY - A combination of a NOT NULL and UNIQUE. Uniquely identifies each row in a table
4. FOREIGN KEY - Prevents actions that would destroy links between tables
5. CHECK - Ensures that the values in a column satisfies a specific condition
6. DEFAULT - Sets a default value for a column if no value is specified

Syntax
``` SQL
-- With creation of table
CREATE TABLE table_name (
    column1 datatype constraint,
    column2 datatype constraint,
    column3 datatype constraint,
    ....
);

-- After creation of table
  ALTER TABLE table_name ALTER column_name datatype constraint -> ( NOT NULL, DEFAULT)
  ALTER TABLE table_name ADD UNIQUE(columns_names) -> UNIQUE
  ALTER TABLE table_name ADD PRIMARY KEY(columns_names) -> PRIMARY KEY
  ALTER TABLE table_name1 ADD FOREIGN KEY (column_name_table1) REFERENCES table_name2(column_name_table2) -> FOREIGN KEY
  ALTER TABLE table_name ADD CHECK (column_name condition);
```

Optimization, Performance, Resource
``` SQL
SELECT OBJECT_NAME(IX.OBJECT_ID) Table_Name
	   ,IX.name AS Index_Name
	   ,IX.type_desc Index_Type
	   ,SUM(PS.[used_page_count]) * 8 IndexSizeKB
	   ,IXUS.user_seeks AS NumOfSeeks
	   ,IXUS.user_scans AS NumOfScans
	   ,IXUS.user_lookups AS NumOfLookups
	   ,IXUS.user_updates AS NumOfUpdates
	   ,IXUS.last_user_seek AS LastSeek
	   ,IXUS.last_user_scan AS LastScan
	   ,IXUS.last_user_lookup AS LastLookup
	   ,IXUS.last_user_update AS LastUpdate
FROM sys.indexes IX
INNER JOIN sys.dm_db_index_usage_stats IXUS ON IXUS.index_id = IX.index_id AND IXUS.OBJECT_ID = IX.OBJECT_ID
INNER JOIN sys.dm_db_partition_stats PS on PS.object_id=IX.object_id
WHERE OBJECTPROPERTY(IX.OBJECT_ID,'IsUserTable') = 1
AND IX.type_desc = 'NONCLUSTERED'
--AND OBJECT_NAME(IX.OBJECT_ID) ='cmCommissionCredit'
AND IXUS.user_seeks < 100
AND IX.name NOT LIKE '%core%'
GROUP BY OBJECT_NAME(IX.OBJECT_ID) ,IX.name ,IX.type_desc ,IXUS.user_seeks ,IXUS.user_scans ,IXUS.user_lookups,IXUS.user_updates ,IXUS.last_user_seek ,IXUS.last_user_scan ,IXUS.last_user_lookup ,IXUS.last_user_update
ORDER BY IXUS.user_updates desc
```

#### Pagination in SQL

Pagination is a process that is used to divide a large data into smaller discrete pages, and this process is also known as paging. Pagination is commonly used by web applications and can be seen on Google. When we search for something on Google, it shows the results on the separated page; this is the main idea of the pagination.

What is Pagination in SQL Server?

In terms of the SQL Server, the aim of the pagination is, dividing a resultset into discrete pages with the help of the query. When the **OFFSET** and **FETCH** arguments are used in with the **ORDER BY** clause in a **SELECT** statement, it will be a pagination solution for SQL Server.

**OFFSET** argument specifies how many rows will be skipped from the resultset of the query. And we need to take care of all possibilities of values that **OFFSET** can handle:

- **0** - When we set **OFFSET** value as **0**, no rows will be skipped from the resultset.
- **> Max Rows in Table** - On the other hand, if we set the **OFFSET** value, which is greater than the total row number of the result set, no rows will be displayed on the result. 

**FETCH** argument specifies how many rows will be displayed in the result, and the **FETCH** argument must be used with the **OFFSET** argument. 

**Tip:** The **TOP CLAUSE** limits the number of rows that returned from the **SELECT** statement. When we use the **TOP** clause without **ORDER BY**, it can be returned to arbitrary results.

After figuring out the answer to **“What is Pagination?”** question, we will learn how we can write a pagination query in SQL Server. At first, we will execute the following query and will tackle the query:

Syntax
```SQL
DECLARE @PageNumber AS INT
DECLARE @RowsOfPage AS INT

SET @PageNumber=2
SET @RowsOfPage=4

SELECT Column1, Column2, ... FROM TableName
ORDER BY Column_X
OFFSET (@PageNumber-1)*@RowsOfPage ROWS
FETCH NEXT @RowsOfPage ROWS ONLY
```

In the following example, the query will skip the first 3 rows of the dbo.PaginationTest table and then return all remaining rows.

```SQL
-- Generation of Test Table

CREATE TABLE dbo.PaginationTest ( Id INT PRIMARY KEY IDENTITY(1,1), FruitName VARCHAR(50), Price INT )

INSERT INTO dbo.PaginationTest VALUES('Apple',20)
INSERT INTO dbo.PaginationTest VALUES('Apricot',12)
INSERT INTO dbo.PaginationTest VALUES('Banana',8)
INSERT INTO dbo.PaginationTest VALUES('Cherry',11)
INSERT INTO dbo.PaginationTest VALUES('Strawberry',26)
INSERT INTO dbo.PaginationTest VALUES('Lemon',4)  
INSERT INTO dbo.PaginationTest VALUES('Kiwi',14)  
INSERT INTO dbo.PaginationTest VALUES('Coconut',34) 
INSERT INTO dbo.PaginationTest VALUES('Orange',24)  
INSERT INTO dbo.PaginationTest VALUES('Raspberry',13)
INSERT INTO dbo.PaginationTest VALUES('Mango',9)
INSERT INTO dbo.PaginationTest VALUES('Mandarin',19)
INSERT INTO dbo.PaginationTest VALUES('Pineapple',22)

SELECT * FROM dbo.PaginationTest

-- Example
DECLARE @PageNumber AS INT
DECLARE @RowsOfPage AS INT

SET @PageNumber=2
SET @RowsOfPage=4

SELECT FruitName, Price FROM dbo.PaginationTest
ORDER BY Price 
OFFSET (@PageNumber-1)*@RowsOfPage ROWS
FETCH NEXT @RowsOfPage ROWS ONLY

/*
    As we can see, we have declared two variables in the above query, and these variables are:

    1. @PageNumber – It specifies the number of the page which will be displayed
    2. @RowsOfPage – It specifies how many numbers of rows will be displayed on the page. As a result, the SELECT statement displays the second page, which contains 4 rows
*/

-- Example Pagination in LOOP
DECLARE @PageNumber AS INT, @RowsOfPage AS INT, @MaxTablePage  AS FLOAT 

SET @PageNumber=1
SET @RowsOfPage=4

SELECT @MaxTablePage = COUNT(*) FROM dbo.PaginationTest
        
SET @MaxTablePage = CEILING(@MaxTablePage/@RowsOfPage)
        
WHILE @MaxTablePage >= @PageNumber BEGIN
      SELECT FruitName, Price 
      FROM dbo.PaginationTest
      ORDER BY Price 
      OFFSET (@PageNumber-1)*@RowsOfPage ROWS
      FETCH NEXT @RowsOfPage ROWS ONLY
      
      SET @PageNumber = @PageNumber + 1
END
```

As you can see in the last Example, we created a pretty simple formula. At first, we assigned the total row number of the dbo.PaginationTest table to the @MaxTablePage variable, and then we divided it into how many rows will be displayed on a page. So, we have calculated the number of pages that will be displayed. However, the calculated value can be a decimal, and for that, we used the CEILING function to round it up to the smallest integer number that is bigger than the calculated number. As a second step, we implemented a WHILE-LOOP and iterated @PageNumber variable until the last page of the number.



###In this part we will learn Database structure for **XXX Product Suite**
 
####Tables

The name of each table begins with the first letters of the module to which they belong, by that we can easily finds and use them in writing queries.

|**Prefix**|**Module**|**Example**|
|-|-|-|
|r|Registers|rContact|
|cd|Cash Management|cdCashDesk|
|crm|Customer Relationship Management (CRM)|crmLead|
|lo|Loan|loApplication|
|cms|Collateral Management|cmdCollateral
|cm|Commission|cmCommission|
|acc|Accounts|accAccount|
|pmt|Payment Operations|pmtPayment|
|crd|Cards|crdCard|
|chq|Cheque Managment|chqCheque|
|aml|Anti-Money Laundering (AML)|amlIndicator|
|inv|Invoicing|invInvoiceType|
|fa|Inventory Managment|faFixedAsset|
|gl|Accounting|glAccount|

####Procedures and Functions

- **Procedures**

The names of all procedures in **XXX Product Suite** Database use the same prefixes as Tables syntax for generation is

```SQL
CREATE OR ALTER PROCEDURE [dbo].[Prefix_NameOfProcedure_sp]  ListOfParameters
--WITH ENCRYPTION
AS
/********************************************************************************************************
<Version>1.0.0</Version>

* Description:
	Description what is the meaning and purpose of this procedure

* Input parameters:
		Description for each Input Parameter for this procedure

* Output parameters:
	Description for each Output Parameter for this procedure

* Created:
	Date of creation - Name of the person who created it

* Change
        Date of change - Name of the person who changed it

* Example calling:				
       Call example

* Notes:
      Additional Note for procedure
********************************************************************************************************/
```

 List of most important procedures in **XXX Product Suite**

1. Combo_RelationType
2. DynamicMenu_GetItems_RegistersContactPersonEdit_sp
3. bComboLoanOfficer
4. bComborOffice
5. **bFormAfterSave**
6. **bFormBeforeDelete**
7. **bFormModeByLevel**
8. bags_return_TranslatedMessage_sp
9. bags_return_getCounter_sp
10. bags_rollback_void_sp
11. bcFillFormBankClient_2
12. bcFillPersonDetails_2
13. bcFillPersonDetails_2Info
14. rGenerateAutoTransactionID_sp
15. rGenerateAutoTransactionItem
16. rInsertTransactionVoid_sp
17. r_VoidrAutoTransaction_sp
18. r_batch_StatusChange_sp
19. r_change_rAgreement_Status_sp
20. r_insert_rTransactionById_sp
21. r_insert_rTransaction_sp
22. r_rClient_manage_sp
23. r_return_IsInRange_sp
24. r_return_QuestionnaireByActivityKey_sp
25. bFormAfterSave_ValidationConditionEdit
26. bFormAfterSave_ValidationReferenceEdit
27. bFormModeByLevel_Validation
28. vld_vldValidation_Reference_Navigation_sp
29. GetCoreUserDetails
30. GetExplicitRoleClaims
31. GetExplicitUserClaims
32. GetUserClaimsByDevice
33. GetUserDataPrivilege
34. GetUserPrivilege
35. RegisterUserLogin

The Bolded three procedures are the main procedures which we use on every Save on evry form in XXX Solution.


- **Functions**

The names of all functions in **XXX Product Suite** Database use the same prefixes as Tables syntax for generation is

```SQL
CREATE/ALTER FUNCTION [dbo].[Prefix_NameOfProcedure_sp]  (ListOfParameters)
RETURN ResultType (INT, BIT, DECIMAL,... )
--WITH ENCRYPTION
AS
/********************************************************************************************************
<Version>1.0.0</Version>

* Description:
	Description what is the meaning and purpose of this function

* Input parameters:
		Description for each Input Parameter for this function

* Output parameters:
	Description for each Output Parameter for this function

* Created:
	Date of creation - Name of the person who created it

* Change
        Date of change - Name of the person who changed it

* Example calling:				
       Call example

* Notes:
      Additional Note for function
********************************************************************************************************/
```
 List of most important functions in **XXX Product Suite**

1. bagsProjectStartDate_f
2. bags_return_DDCAnswer_f
3. bags_return_Message_f
4. bags_return_NumericForMessage_f
5. ini_Datum
6. r_get_AplParamClient_f
7. r_get_AplParamDef_f
8. r_return_ContactPhone_f
9. r_return_ExchangeRateonDateFromHeader_f
10. r_return_GlobalItemValidation_f
11. r_return_OfficialExchangeRateOnDate_f
12. r_return_SegmentOnDate_f
13. convert_date

####Pagination in XXX Solution Reports
In **XXX Solution** Reports module for pagination we have already calculated values for OFFSET and FETCH parts of pagination and they are stored in dbo.bagsReportCallFilter with ColumnAlias **Skip** and **Take**

If it's not clear by now :

1. **Take**  - How much data should we 'take' from the table. How much rows.
2. **Skip**  - How much data should we 'skip' from the table. How much rows.

Example how to get and how to use **Skip** and **Take** in the reports
```SQL
DECLARE @SQL_Final AS NVARCHAR(MAX)
DECLARE @Skip NVARCHAR(MAX) = NULL
DECLARE @Take NVARCHAR(MAX) = NULL

SELECT @Skip = Value FROM dbo.bagsReportCallFilter WHERE bagsReportCallID = @bagsReportID AND ColumnAlias = 'Skip'
SELECT @Take = Value FROM dbo.bagsReportCallFilter WHERE bagsReportCallID = @bagsReportID AND ColumnAlias = 'Take'
 

SET @SQL_Final = N'
    SELECT * 
    FROM dbo.rptClientsReport c
    WHERE rLanguageid = @LanguageID AND ' + @WherePart + ' 
    ORDER BY rContactID
    OFFSET ' + CAST(@Skip AS NVARCHAR(10)) + ' ROWS FETCH NEXT ' + CAST(@Take AS NVARCHAR(10)) + ' ROWS ONLY;';
```

Example of one procedure
```SQL
SET QUOTED_IDENTIFIER ON
SET ANSI_NULLS ON
GO


CREATE OR ALTER PROCEDURE dbo.CCR_ClientsReport_development_sp @bagsReportID INT, @ID  INT, @Parameter NVARCHAR(100)
/*
	<Version>1.0.1</Version>
*/
WITH ENCRYPTION
AS
BEGIN
  SET DATEFORMAT DMY;
  SET NOCOUNT ON;
  SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;

  DECLARE @LanguageID INT
  DECLARE @SQL_Final AS NVARCHAR(MAX)
  DECLARE @Skip NVARCHAR(MAX) = NULL
  DECLARE @Take NVARCHAR(MAX) = NULL
  DECLARE @Count NVARCHAR(MAX) = NULL

  SELECT @LanguageID = LanguageID from bagsReportCall where bagsReportCallID = @bagsReportID
  SELECT @Skip = Value FROM dbo.bagsReportCallFilter WHERE bagsReportCallID = @bagsReportID AND ColumnAlias = 'Skip'
  SELECT @Take = Value FROM dbo.bagsReportCallFilter WHERE bagsReportCallID = @bagsReportID AND ColumnAlias = 'Take'
  SELECT @Count = Value FROM dbo.bagsReportCallFilter WHERE bagsReportCallID = @bagsReportID AND ColumnAlias = 'Count'

  IF @LanguageID IS NULL
    SET @LanguageID = 1033

  DECLARE @WherePart AS Nvarchar(MAX)
  
  IF @WherePart IS NULL OR @WherePart = ''
	  BEGIN
		  SELECT @WherePart = dbo.f_GenWherePart (@bagsReportID, 1)
	  END	

	IF @Count IS NOT NULL BEGIN
	    SET @SQL_Final = N'
		SELECT  COUNT(*)  FROM  dbo.rptClientsReport c
		WHERE rLanguageid = @LanguageID AND' + @WherePart
	     
            EXECUTE sp_executesql @SQL_Final, N'@LanguageID INT', @LanguageID

	    RETURN
	END

	IF ( @Skip IS NULL OR @Take IS NULL )
	BEGIN
	  SET @SQL_Final = N'
		SELECT  *  FROM dbo.rptClientsReport c
		WHERE rLanguageid = @LanguageID AND ' + @WherePart
	END
	ELSE
	BEGIN
	      SET @SQL_Final = N'
	      SELECT * 
	      FROM rptClientsReport c
	      WHERE rlanguageid = @LanguageID AND ' + @WherePart + ' 
              ORDER BY rContactId
	      OFFSET ' + CAST(@Skip AS NVARCHAR(10)) + ' ROWS FETCH NEXT ' + CAST(@Take AS NVARCHAR(10)) + ' ROWS ONLY;';
	END

  EXECUTE sp_executesql @SQL_Final, N'@LanguageID INT', @LanguageID

END

GO
```

#### Resources
- [W3Schools - Introduction to SQL](https://www.w3schools.com/sql/sql_intro.asp)
- [SQLBolt   - Learn SQL with simple, interactive exercises](https://sqlbolt.com/)
- [sql-practice](https://www.sql-practice.com/)
- 
- [SQL Server tutorial for beginners](https://www.youtube.com/playlist?list=PL08903FB7ACA1C2FB)
- [GeeksForGeeks - What is SQL](https://www.geeksforgeeks.org/what-is-sql/?ref=header_search)
- [Learning SQL By Stack Overflow](https://www.linkedin.com/posts/learning-sql_learning-sql-free-ebook-from-stack-overflow-activity-7059489276673081344-0fS_?utm_source=share&utm_medium=member_desktop)
- [Learning SQL By Alan Beaulien](http://www.r-5.org/files/books/computers/languages/sql/mysql/Alan_Beaulieu-Learning_SQL-EN.pdf)
- [SQL for beginners - Microsoft SQL Server](https://lnkd.in/drU-RbQm)
- [Query optimization techniques in SQL](https://lnkd.in/d94fcHhQ)
- [Learn SQL from Basic](https://lnkd.in/dPaN7Dhc)
- [Microsoft SQL for Beginners (SQL Server and T-SQL)](https://lnkd.in/dT325WhH)
- [SQL 101: A Beginners Guide to SQL](https://lnkd.in/deg_h3Ev)
- [SQL Foundations](https://lnkd.in/dka27VCS)
- [SQL Practice](https://www.sql-practice.com)
### Knowledge Check
