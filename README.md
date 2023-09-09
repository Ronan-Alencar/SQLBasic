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
