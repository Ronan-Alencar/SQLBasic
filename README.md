# SQL/T-SQL
SQL/T-SQL Queries

SELECT AND FILTER

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

AGGREGATE FUNCTIONS

```SQL

--AGGREGATE FUNCTIONS

--MAX

SELECT MAX (TaxRate)
FROM Sales.SalesTaxRate
GROUP BY TaxType;

--AVG

SELECT AVG ( ISNULL (Weight, 0) ) AS 'AvgWeight'
FROM Production.Product;

--COUNT

SELECT COUNT (*)
FROM Person.Person;

--GROUP BY + SUM

SELECT
 SalesOrderID,
 SUM (LineTotal) AS SubTotal
FROM Sales.SalesOrderDetail
GROUP BY SalesOrderID
ORDER BY SalesOrderID;

--GROUP BY + COUNT

SELECT
 A.City,
 COUNT (E.BusinessEntityID) AS EmployeeCount
FROM HumanResources.Employee E
INNER JOIN Person.Address A ON E.BusinessEntityID = A.AddressID
GROUP BY A.City
ORDER BY A.City;

--HAVING

SELECT
 SalesOrderId,
 SUM (LineTotal) AS SubTotal
FROM Sales.SalesOrderDetail
GROUP BY SalesOrderID
HAVING SUM (LineTotal) > 100000
ORDER BY SalesOrderID;

--ROLLUP

SELECT
 ProductId,
 Shelf,
 SUM (Quantity) AS QtySum
FROM Production.ProductInventory
WHERE ProductID < 6
GROUP BY ROLLUP (ProductID, Shelf);

--CUBE

SELECT
 ProductID,
 Shelf,
 SUM (Quantity) AS QtySum
FROM Production.ProductInventory
WHERE ProductID < 6
GROUP BY CUBE (ProductId, Shelf);

--RANK

SELECT
 P.Name,
 P.ListPrice,
 PSC.Name,
 RANK() OVER (PARTITION BY PSC.Name ORDER BY P.ListPrice DESC) AS PriceRank
FROM Production.Product P
JOIN Production.ProductSubcategory PSC ON P.ProductSubcategoryID = PSC.ProductSubcategoryID

--DENSE_RANK

SELECT
 P.Name,
 P.ListPrice,
 PSC.Name,
 DENSE_RANK() OVER (PARTITION BY PSC.Name ORDER BY P.ListPrice DESC) AS PriceRank
FROM Production.Product P
JOIN Production.ProductSubcategory PSC ON P.ProductSubcategoryID = PSC.ProductSubcategoryID;

--ROW_NUMBER

SELECT
 ROW_NUMBER() OVER (PARTITION BY PSC.Name ORDER BY P.ListPrice) AS Row,
 P.Name,
 P.ListPrice,
 PSC.Name
FROM Production.Product P
JOIN Production.ProductSubcategory PSC ON P.ProductSubcategoryID = PSC.ProductSubcategoryID;

--NTILE

SELECT
 NTILE(3) OVER (PARTITION BY PSC.Name ORDER BY P.ListPrice) AS PriceBand,
 PC.Name,
 P.Name,
 P.ListPrice
FROM Production.Product P
JOIN Production.ProductSubcategory PSC ON P.ProductSubcategoryID = PSC.ProductSubcategoryID
JOIN Production.ProductCategory PC ON PSC.ProductCategoryID = PC.ProductCategoryID;

--PIVOT/UNPIVOT

SELECT
 DaysToManufacture,
 AVG (StandardCost) AS AverageCost
FROM Production.Product
GROUP BY DaysToManufacture

SELECT
 'AverageCost' AS Cost_Sorted_By_Production_Days,
 [0], [1], [2], [3], [4]
FROM 
(SELECT
  DaysToManufacture,
  StandardCost
  FROM Production.Product
) AS SourceTable
PIVOT
(
AVG (StandardCost)
FOR DaysToManufacture IN ([0], [1], [2], [3], [4])
) AS PivotTable;
```
JOINS

```SQL
--JOIN

--INNER JOIN

SELECT
 e.LoginID
FROM HumanResources.Employee AS e
INNER JOIN Sales.SalesPerson AS s
ON e.BusinessEntityID = s.BusinessEntityID;

--LEFT OUTER JOIN

SELECT
 p.Name,
 pr.ProductReviewID
FROM Production.Product P
LEFT OUTER JOIN Production.ProductReview pr ON p.ProductID = pr.ProductID;

--CROSS JOIN

SELECT
 p.BusinessEntityID,
 t.Name AS Territory
FROM Sales.SalesPerson p
CROSS JOIN Sales.SalesTerritory t
ORDER BY BusinessEntityID;

--JOIN with 3 tables

SELECT
 p.Name,
 v.Name
FROM Production.Product p
JOIN Purchasing.ProductVendor pv ON p.ProductID = pv.ProductID
JOIN Purchasing.Vendor v ON pv.BusinessEntityID = v.BusinessEntityID
WHERE ProductSubcategoryID = 15
ORDER BY v.Name;

--UNION, Unify the distinct lines

SELECT
 ProductId
FROM Production.Product
UNION
SELECT
 ProductID
FROM Production.WorkOrder;

--UNION ALL, unify all the lines

SELECT
 ProductId
FROM Production.Product
UNION ALL
SELECT
 ProductID
FROM Production.WorkOrder;

--EXCEPT

SELECT
 ProductId
FROM Production.Product
EXCEPT
SELECT
 ProductID
FROM Production.WorkOrder;

--INTERSECT

SELECT
 ProductId
FROM Production.Product
INTERSECT
SELECT
 ProductID
FROM Production.WorkOrder;

--TOP

SELECT TOP (5)
 FirstName,
 LastName
FROM Person.Person
ORDER BY Person.FirstName;

SELECT TOP 5 PERCENT
 FirstName,
 LastName
FROM Person.Person
ORDER BY Person.FirstName;

--TABLESAMPLE, generates random sample

SELECT
 FirstName,
 LastName
FROM Person.Person
TABLESAMPLE (5 PERCENT)
ORDER BY Person.FirstName;
```

SUBQUERIES/TEMP TABLES/CTEs

```SQL
--SUBQUERIES

SELECT
 ProductID,
 Name
FROM Production.Product
WHERE Color NOT IN
(SELECT Color
FROM Production.Product
WHERE ProductID = 5);

SELECT
 Name,
 ListPrice,
 (SELECT AVG (ListPrice) FROM Production.Product) AS Average,
 ListPrice - (SELECT AVG (ListPrice) FROM Production.Product) AS Difference
FROM Production.Product
WHERE ProductSubcategoryID = 1;

SELECT
 Name,
 ListPrice
FROM Production.Product
WHERE ListPrice >= SOME
(SELECT MAX (ListPrice)
 FROM Production.Product
 GROUP BY ProductSubcategoryID);

SELECT
 Name,
 ListPrice
FROM Production.Product
WHERE ListPrice >= ALL
(SELECT MAX (ListPrice)
 FROM Production.Product
 GROUP BY ProductSubcategoryID);

--EXISTS

SELECT
 Name
FROM Production.Product
WHERE EXISTS
(SELECT *
 FROM Production.ProductSubcategory
 WHERE ProductSubcategoryID = Production.Product.ProductSubcategoryID
 AND NAME = 'Wheels');

--TEMPORARY TABLES

--#StoreInfoLocal/##StoreInfoGlobal

CREATE TABLE #StoreInfoLocal
(
EmployeeID INT,
ManagerID INT,
Num INT
);

INSERT INTO #StoreInfoLocal (EmployeeID, ManagerID, Num) VALUES (1, 2, 3);

SELECT * FROM #StoreInfoLocal;

DROP TABLE #StoreInfoLocal;

--CTE

WITH TopSales (SalesPerson, NumSales) AS
 (SELECT SalesPersonID, COUNT(*)
 FROM Sales.SalesOrderHeader
 GROUP BY SalesPersonID)

SELECT
 LoginID,
 NumSales
FROM HumanResources.Employee e
INNER JOIN TopSales ON TopSales.SalesPerson = e.BusinessEntityID
ORDER BY NumSales DESC;
```

DML (INSERT, DELETE, UPDATE) & DTL (TRANSACTION, COMMIT, ROLLBACK)

```SQL
--INSERT

INSERT INTO Production.UnitMeasure (UnitMeasureCode, Name, ModifiedDate) VALUES (N'F2', N'Square Feet', GETDATE());

INSERT INTO Production.UnitMeasure
VALUES (N'F3', N'Square Feet 3', GETDATE()),
       (N'Y2', N'Square Yards', GETDATE());

SELECT *
FROM Production.UnitMeasure
ORDER BY ModifiedDate DESC;

--INSERT with Identity

CREATE TABLE dbo.T1 (
column_1 INT IDENTITY,
column_2 VARCHAR (30)
)
GO

INSERT INTO dbo.T1 VALUES ('Row #1');
INSERT INTO dbo.T1 VALUES ('Row #2');
GO

SELECT * FROM dbo.T1;

--SET IDENTITY, to manipulate the id column

SET IDENTITY_INSERT dbo.T1 ON;
GO

INSERT INTO dbo.T1 (column_1, column_2) VALUES (-99, 'Explicity Identity');

SELECT * FROM dbo.T1;

--DELETE/TRUNCATE

DELETE 
FROM HumanResources.JobCandidate
WHERE 'term'
;

TRUNCATE TABLE HumanResources.JobCandidate;

--UPDATE, 'N' used to specify NCHAR, VARCHAR O NTEXT value

UPDATE Sales.SalesPerson SET Bonus = 6000;

UPDATE Production.Product
SET Color = N'Metallic Red'
WHERE Name LIKE N'Road-250%' AND Color = N'Red';

SELECT *
FROM Production.Product
WHERE Name LIKE N'Road-250%'

--TRANSACTION

BEGIN TRAN T1

SELECT MAX(Bonus) FROM Sales.SalesPerson

UPDATE Sales.SalesPerson SET Bonus = 6000;

SELECT MAX(Bonus) FROM Sales.SalesPerson

ROLLBACK TRAN T1

SELECT MAX(Bonus) FROM Sales.SalesPerson
```

METADATA & XML

```SQL
--METADATA

SELECT
 name,
 object_id,
 type_desc
FROM sys.tables;

--INFORMATION_SCHEMA (ISO)

SELECT
 COLUMN_NAME
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = N'Product';

--DMV

SELECT
 cpu_count,
 sqlserver_start_time
FROM sys.dm_os_sys_info;

--SYSTEM STORED PROCEDURES

EXEC sp_columns @table_name = N'Department',
@table_owner = N'HumanResourcers'

--SYSTEM FUNCTIONS

SELECT
 COLUMNPROPERTY (OBJECT_ID('Person.Person'), 'LastName', 'PRECISION') AS 'Column Length';

--XML

SELECT
 Cust.CustomerID,
 OrderHeader.SalesOrderID,
 OrderHeader.Status,
 Cust.PersonID
FROM Sales.Customer Cust
INNER JOIN Sales.SalesOrderHeader OrderHeader ON (Cust.CustomerID = OrderHeader.CustomerID)
ORDER BY Cust.CustomerID
FOR XML AUTO;
```
VIEW

```SQL
-- VIEW

SELECT
 FirstName,
 LastName,
 PersonType
FROM Person.Person;

CREATE VIEW Person.vPerson AS
SELECT
 FirstName,
 LastName,
 PersonType
FROM Person.Person
GO
 
SELECT *
FROM Person.vPerson;

DROP VIEW Person.vPerson;

```
