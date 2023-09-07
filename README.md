# SQLBasic
SQL/T-SQL Basic Queries

```SQL
USE AdventureWorks2017
GO

--SELECTING AND FILTERING DATA

--SELECT

SELECT *
FROM Person.Person;

SELECT FirstName, LastName, ModifiedDate
FROM Person.Person;

--WHERE

SELECT
 BusinessEntityID AS 'EmployeeIndentificationNumber',
 HireDate,
 VacationHours,
 SickLeaveHours
FROM HumanResources.Employee
WHERE HireDate > '2000-01-01';

SELECT
 FirstName,
 LastName,
 ModifiedDate
FROM Person.Person
WHERE MiddleName IS NULL;

--CAST

SELECT
 CAST (OrderDate AS DATE) AS OrderDate,
 AccountNumber,
 SubTotal,
 TaxAmt
FROM Sales.SalesOrderHeader
WHERE OrderDate >= '2001-08-01'

-- IN

SELECT
 SalesOrderID,
 OrderQty,
 ProductID,
 UnitPrice
FROM Sales.SalesOrderDetail
WHERE ProductID IN (740, 738, 760, 755)

--NULL

SELECT
 Description,
 DiscountPct,
 MaxQty,
 ISNULL (MaxQty, 0) AS MaxQuantity
FROM Sales.SpecialOffer;

--COALESCE

SELECT
 Description,
 DiscountPct,
 MaxQty,
 MinQty,
 COALESCE (MaxQty, MinQty, 0) AS Quantity
FROM Sales.SpecialOffer;

--ORDER BY

SELECT
 FirstName,
 LastName,
 MiddleName
FROM Person.Person
ORDER BY LastName, FirstName;

--DISTINCT

SELECT DISTINCT
 FirstName,
 LastName,
 MiddleName
FROM Person.Person
ORDER BY LastName, FirstName;
```
