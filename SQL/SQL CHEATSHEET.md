# SQL CHEATSHEET

SQL is a standard language for storing, manipulating and retrieving data in databases.

Structured Query Language, not case sensitive.

**QUERY**

SELECT \* FROM _table\_name WHERE condition_ ORDER BY _column1, column2_ ASC|DESC;

aritmetic operators:    +, - , \* , /. %
logical operators: =   >   <   >=   <=    <>    BETWEEN  (specify a range)  LIKE (string pattern)    IN

example:
```sql
SELECT * FROM Products
WHERE Price BETWEEN 50 AND 60
OR Date IS NULL    --null check
AND City LIKE 's%';    --starts with s
AND Name IN ('Mauro','Marco')    --IN lets you check for a set of possible vlaues, generally IN contains a subquery returning a subset of IDs
```

in SQL server sintax: (the wildcards are specific to the SQL flavour)
![[SQL/_resources/SQL_CHEATSHEET.resources/image.png]]

SELECT DISTINCT _column1_, _column2, ..._    
FROM _table\_name_;         _//only elements that have different content of the columns_

only top n rows:
SELECT TOP _number_
FROM _table\_name;_

**AGGREGATE FUNCTIONS**
_select max, min, avg, sum, count occurences_
SELECT MIN | MAX | AVG | SUM | COUNT (_column\_name_)    --the column must correspond to a numeric type
FROM _table\_name_
WHERE _condition_;

Join takes columns from multiple tables based on a common column
SELECT Orders.OrderID, Customers.CustomerName, Orders.OrderDate
FROM Orders INNER JOIN Customers ON Orders.CustomerID=Customers.CustomerID;

* `(INNER) JOIN`: Returns records that have matching values in both tables
* `LEFT (OUTER) JOIN`: Returns all records from the left table, and the matched records from the right table
* `RIGHT (OUTER) JOIN`: Returns all records from the right table, and the matched records from the left table
* `FULL (OUTER) JOIN`: Returns all records when there is a match in either left or right table

You can also perform self join on the same table,
example: retrieve the customers from the same city:
```sql
SELECT A.CustomerName AS CustomerName1, B.CustomerName AS CustomerName2, A.City
FROM Customers A, Customers B
WHERE A.CustomerID <> B.CustomerID
AND A.City = B.City
ORDER BY A.City;
```

UNION: (possible only when the two subqueries returns the same amount of columns with the same datatype)
the union operator is only adding the distincts values, in order to add also the copies use: _UNION ALL_

SELECT _column\_name(s)_ FROM _table1_
UNION
SELECT _column\_name(s)_ FROM _table2_;

GROUP BY is used to group results based of values inside a column:
ex: select the number of customer in each country:
SELECT COUNT(CustomerID), Country
FROM Customers
GROUP BY Country
ORDER BY COUNT(CustomerID) DESC;

after a GROUP BY you need to use HAVING instead of WHERE (having works with aggregate functions)
SELECT COUNT(CustomerID), Country
FROM Customers
GROUP BY Country
HAVING COUNT(CustomerID) > 5;

**ANY/ALL/EXISTS (used in where/having)**
ANY returns true if any of the results of the subquery match the condition

SELECT _column\_name(s)_
FROM _table\_name_
WHERE _column\_name operator_ ANY
  (SELECT _column\_name_
  FROM _table\_name_
  WHERE _condition_);

ALL is the same as ANY but returns true only if all the rows of the subquey are matching the condition
Exists is the same but it only needs at least one row in the subquery result to return true

SWITCH case in SQL:
_SELECT \* FROM table_
CASE
    WHEN _condition1_ THEN _result1_
    WHEN _condition2_ THEN _result2_
    WHEN _conditionN_ THEN _resultN_
    ELSE _result_
END;

**INSERT & UPDATE**

```sql
INSERT INTO _table_name_
VALUES (_value1_, _value2_, _value3_, ...);

UPDATE _table_name_
SET _column1_ = _value1_, _column2_ = _value2_, ...
WHERE _condition_;
```

In this case we query a table and we put the result into a newtable
```sql
SELECT *
INTO _newtable_ [IN _externaldb_]
FROM _oldtable_
WHERE _condition_;

CREATE TABLE tablewname (
column name column type constraint,
...
)

ALTER TABLE table name
ADD column name column type constraint

ALTER TABLE table name
DROP COLUMN column name

ALTER TABLE table name
RENAME COLUMN columnName TO newname

ALTER TABLE tablename
MODIFY COLUMN columnName newDataType

CREATE DATABASE dbname;
```

**DELETE**

```sql
DELETE FROM _table_name_  WHERE _condition_;     --deletes certain rows

DROP TABLE _table_name_;

DELETE DATABASE dbName;
```

**ALIAS**
they give temporary names to table or column
```sql
SELECT _column_name_ AS _alias_name
FROM _table_name;

SELECT _column_name(s)
FROM _table_name_ AS _alias\name;
```

**INDEX**
indexes are elements not visible from the DB structure, they change only the performance, you create an index when you need more performance on a specific column:

```sql
CREATE INDEX index_name
ON table_name (column_name)

-- and then you can delete if
DROP INDEX _table_name_._index_name_;
```

**STORED PROCEDURES**

A stored procedure is a prepared SQL code that you can save, so the code can be reused over and over again.
So if you have an SQL query that you write over and over again, save it as a stored procedure, and then just call it to execute it.

```sql
CREATE PROCEDURE _procedure_name
AS
_sql_statement
GO;
```

then run the procedure:
`EXEC _procedre_name;`

**CONSTRAINTS**

* [NOT NULL](https://www.w3schools.com/sql/sql_notnull.asp) - Ensures that a column cannot have a NULL value
* [UNIQUE](https://www.w3schools.com/sql/sql_unique.asp) - Ensures that all values in a column are different
* [PRIMARY KEY](https://www.w3schools.com/sql/sql_primarykey.asp) - A combination of a `NOT NULL` and `UNIQUE`. Uniquely identifies each row in a table. for a primary key you can also specify the the autoincrement option i different ways based on the DBMS
* [FOREIGN KEY](https://www.w3schools.com/sql/sql_foreignkey.asp) - Prevents actions that would destroy links between tables

		A `FOREIGN KEY` is a field (or collection of fields) in one table, that refers to the [PRIMARY KEY](https://www.w3schools.com/sql/sql_primarykey.asp) in another table.
		example:
```sql
CREATE TABLE Orders (
    OrderID int NOT NULL PRIMARY KEY,
    OrderNumber int NOT NULL,
    PersonID int FOREIGN KEY REFERENCES Persons(PersonID)
);
```
		

* [CHECK](https://www.w3schools.com/sql/sql_check.asp) - Ensures that the values in a column satisfies a specific condition

```sql
CREATE TABLE Persons (
    ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int CHECK (Age>=18)
);
```

* [DEFAULT](https://www.w3schools.com/sql/sql_default.asp) - Sets a default value for a column if no value is specified

```sql
CREATE TABLE Persons (
    ID int NOT NULL, 
    City varchar(255) DEFAULT 'Sandnes'
);
```

* [CREATE INDEX](https://www.w3schools.com/sql/sql_create_index.asp) - Used to create and retrieve data from the database very quickly

**DATA TYPES AND FUNCTIONS**
they change slightly based on the SQL flavour
<https://www.w3schools.com/sql/sql_datatypes.asp>

most common:
varchar(n)
blob(size)
boolean
int
float
date

FUNCTIONS READY TO BE USED: <https://www.w3schools.com/sql/sql_ref_mysql.asp>
