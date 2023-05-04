
```SQL
SELECT Name, StandardCost, ListPrice
FROM SalesLT.Product;

SELECT Name, ListPrice - StandardCost
FROM SalesLT.Product;

SELECT Name, ListPrice - StandardCost AS Markup
FROM SalesLT.Product;

SELECT ProductNumber, Color, Size, Color + ', ' + Size AS ProductDetails
FROM SalesLT.Product;


SELECT ProductID + ': ' + Name
FROM SalesLT.Product;

-- ##############################################3

SELECT CAST(ProductID AS varchar(5)) + ': ' + Name AS ProductName
FROM SalesLT.Product;

SELECT CONVERT(varchar(5), ProductID) + ': ' + Name AS ProductName
FROM SalesLT.Product;

SELECT SellStartDate,

       CONVERT(nvarchar(30), SellStartDate) AS ConvertedDate,

       CONVERT(nvarchar(30), SellStartDate, 126) AS ISO8601FormatDate

FROM SalesLT.Product;

  

SELECT Name, CAST (Size AS Integer) AS NumericSize

FROM SalesLT.Product; --(note error - some sizes are incompatible)

  

SELECT Name, TRY_CAST (Size AS Integer) AS NumericSize

FROM SalesLT.Product; --(note incompatible sizes are returned as NULL)

-- ################################################

  

SELECT Name, ISNULL(TRY_CAST(Size AS Integer),0) AS NumericSize

FROM SalesLT.Product;

  

SELECT ProductNumber, ISNULL(Color, '') + ', ' + ISNULL(Size, '') AS ProductDetails

FROM SalesLT.Product;

  

SELECT Name, NULLIF(Color, 'Multi') AS SingleColor

FROM SalesLT.Product;

  

SELECT Name, COALESCE(DiscontinuedDate, SellEndDate, SellStartDate) AS FirstNonNullDate

FROM SalesLT.Product;

  

--Searched case

SELECT Name,

        CASE

            WHEN SellEndDate IS NULL THEN 'On Sale'

            ELSE 'Discontinued'

        END AS SalesStatus

FROM SalesLT.Product;

  

--Simple case

SELECT Name,

        CASE Size

            WHEN 'S' THEN 'Small'

            WHEN 'M' THEN 'Medium'

            WHEN 'L' THEN 'Large'

            WHEN 'XL' THEN 'Extra-Large'

            ELSE ISNULL(Size, 'n/a')

        END AS ProductSize

FROM SalesLT.Product;

  
  

-- #######################    MODEL TWO ############################################

  

--Display a list of product colors

SELECT Color FROM SalesLT.Product;

  

--Display a list of product colors with the word 'None' if the value is null

SELECT DISTINCT ISNULL(Color, 'None') AS Color FROM SalesLT.Product;

  

--Display a list of product colors with the word 'None' if the value is null sorted by color

SELECT DISTINCT ISNULL(Color, 'None') AS Color FROM SalesLT.Product ORDER BY Color;

  

--Display a list of product colors with the word 'None' if the value is null and a dash if the size is null sorted by color

SELECT DISTINCT ISNULL(Color, 'None') AS Color, ISNULL(Size, '-') AS Size FROM SalesLT.Product ORDER BY Color;

  
  

--Display the top 100 products by list price

SELECT TOP 100 Name, ListPrice FROM SalesLT.Product ORDER BY ListPrice DESC;

  

--Display the first ten products by product number

SELECT Name, ListPrice FROM SalesLT.Product ORDER BY ProductNumber OFFSET 0 ROWS FETCH NEXT 10 ROWS ONLY;

  

--Display the next ten products by product number

SELECT Name, ListPrice FROM SalesLT.Product ORDER BY ProductNumber OFFSET 10 ROWS FETCH FIRST 10 ROW ONLY;

  

-- ################################

  

--List information about product model 6

SELECT Name, Color, Size FROM SalesLT.Product WHERE ProductModelID = 6;

  

--List information about products that have a product number beginning FR

SELECT productnumber,Name, ListPrice FROM SalesLT.Product WHERE ProductNumber LIKE 'FR%';

  

--Filter the previous query to ensure that the product number contains two sets of two didgets

SELECT Name, ListPrice FROM SalesLT.Product WHERE ProductNumber LIKE 'FR-_[0-9][0-9]_-[0-9][0-9]';

  

--Find products that have no sell end date

SELECT Name FROM SalesLT.Product WHERE SellEndDate IS NOT NULL;

  

--Find products that have a sell end date in 2006

SELECT Name FROM SalesLT.Product WHERE SellEndDate BETWEEN '2006/1/1' AND '2006/12/31';

  

--Find products that have a category ID of 5, 6, or 7.

SELECT ProductCategoryID, Name, ListPrice FROM SalesLT.Product WHERE ProductCategoryID IN (5, 6, 7);

  

--Find products that have a category ID of 5, 6, or 7 and have a sell end date

SELECT ProductCategoryID, Name, ListPrice, SellEndDate FROM SalesLT.Product WHERE ProductCategoryID IN (5, 6, 7) AND SellEndDate IS NULL;

  

--Select products that have a category ID of 5, 6, or 7 and a product number that begins FR

SELECT Name, ProductCategoryID, ProductNumber FROM SalesLT.Product WHERE ProductNumber LIKE 'FR%' OR ProductCategoryID IN (5,6,7);

  

-- #######################    MODEL THREE  ############################################

  

--Basic inner join

SELECT SalesLT.Product.Name As ProductName, SalesLT.ProductCategory.Name AS Category

FROM SalesLT.Product

INNER JOIN SalesLT.ProductCategory

ON SalesLT.Product.ProductCategoryID = SalesLT.ProductCategory.ProductCategoryID;

  

-- Table aliases

SELECT p.Name As ProductName, c.Name AS Category

FROM SalesLT.Product AS p

JOIN SalesLT.ProductCategory As c

ON p.ProductCategoryID = c.ProductCategoryID;

  

-- Joining more than 2 tables

SELECT oh.OrderDate, oh.SalesOrderNumber, p.Name As ProductName, od.OrderQty, od.UnitPrice, od.LineTotal

FROM SalesLT.SalesOrderHeader AS oh

JOIN SalesLT.SalesOrderDetail AS od

ON od.SalesOrderID = oh.SalesOrderID

JOIN SalesLT.Product AS p

ON od.ProductID = p.ProductID

ORDER BY oh.OrderDate, oh.SalesOrderID, od.SalesOrderDetailID;

  

-- Multiple join predicates

SELECT oh.OrderDate, oh.SalesOrderNumber, p.Name As ProductName, od.OrderQty, od.UnitPrice, od.LineTotal

FROM SalesLT.SalesOrderHeader AS oh

JOIN SalesLT.SalesOrderDetail AS od

ON od.SalesOrderID = oh.SalesOrderID

JOIN SalesLT.Product AS p

ON od.ProductID = p.ProductID AND od.UnitPrice = p.ListPrice --Note multiple predicates

ORDER BY oh.OrderDate, oh.SalesOrderID, od.SalesOrderDetailID;

  

--######################

  

--Get all customers, with sales orders for those who've bought anything

SELECT c.FirstName, c.LastName, oh.SalesOrderNumber

FROM SalesLT.Customer AS c

LEFT OUTER JOIN SalesLT.SalesOrderHeader AS oh

ON c.CustomerID = oh.CustomerID

ORDER BY c.CustomerID;

  

--Return only customers who haven't purchased anything

SELECT c.FirstName, c.LastName, oh.SalesOrderNumber

FROM SalesLT.Customer AS c

LEFT OUTER JOIN SalesLT.SalesOrderHeader AS oh

ON c.CustomerID = oh.CustomerID

WHERE oh.SalesOrderNumber IS NULL

ORDER BY c.CustomerID;

  
  

--More than 2 tables

SELECT p.Name As ProductName, oh.SalesOrderNumber

FROM SalesLT.Product AS p

LEFT JOIN SalesLT.SalesOrderDetail AS od

ON p.ProductID = od.ProductID

LEFT JOIN SalesLT.SalesOrderHeader AS oh --Additional tables added to the right must also use a left join

ON od.SalesOrderID = oh.SalesOrderID

ORDER BY p.ProductID;

  
  

SELECT p.Name As ProductName, c.Name AS Category, oh.SalesOrderNumber

FROM SalesLT.Product AS p

LEFT OUTER JOIN SalesLT.SalesOrderDetail AS od

ON p.ProductID = od.ProductID

LEFT OUTER JOIN SalesLT.SalesOrderHeader AS oh

ON od.SalesOrderID = oh.SalesOrderID

INNER JOIN SalesLT.ProductCategory AS c --Added to the left, so can use inner join

ON p.ProductCategoryID = c.ProductCategoryID

ORDER BY p.ProductID;

  

--##########################

--Call each customer once per product

SELECT p.Name, c.FirstName, c.LastName, c.Phone

FROM SalesLT.Product as p

CROSS JOIN SalesLT.Customer as c;

  

--#################

  

-note there's no employee table, so we'll create one for this example

CREATE TABLE SalesLT.Employee

(EmployeeID int IDENTITY PRIMARY KEY,

EmployeeName nvarchar(256),

ManagerID int);

GO

-- Get salesperson from Customer table and generate managers

INSERT INTO SalesLT.Employee (EmployeeName, ManagerID)

SELECT DISTINCT Salesperson, NULLIF(CAST(RIGHT(SalesPerson, 1) as INT), 0)

FROM SalesLT.Customer;

GO

UPDATE SalesLT.Employee

SET ManagerID = (SELECT MIN(EmployeeID) FROM SalesLT.Employee WHERE ManagerID IS NULL)

WHERE ManagerID IS NULL

AND EmployeeID > (SELECT MIN(EmployeeID) FROM SalesLT.Employee WHERE ManagerID IS NULL);

GO

-- Here's the actual self-join demo

SELECT e.EmployeeName, m.EmployeeName AS ManagerName

FROM SalesLT.Employee AS e

LEFT JOIN SalesLT.Employee AS m

ON e.ManagerID = m.EmployeeID

ORDER BY e.ManagerID;

  
  

-- #######################    MODEL FOUR  ############################################

  

-- Setup

CREATE VIEW [SalesLT].[Customers]

as

select distinct firstname,lastname

from saleslt.customer

where lastname >='m'

or customerid=3;

GO

CREATE VIEW [SalesLT].[Employees]

as

select distinct firstname,lastname

from saleslt.customer

where lastname <='m'

or customerid=3;

GO

  

-- Union example

SELECT FirstName, LastName

FROM SalesLT.Employees

UNION

SELECT FirstName, LastName

FROM SalesLT.Customers

ORDER BY LastName;

--############3

  

SELECT FirstName, LastName

FROM SalesLT.Customers

INTERSECT

SELECT FirstName, LastName

FROM SalesLT.Employees;

--#############3

  

SELECT FirstName, LastName

FROM SalesLT.Customers

EXCEPT

SELECT FirstName, LastName

FROM SalesLT.Employees;

  

-- #######################    MODEL FIVE  ############################################

  

-- Scalar functions

SELECT YEAR(SellStartDate) SellStartYear, ProductID, Name

FROM SalesLT.Product

ORDER BY SellStartYear;

  

SELECT YEAR(SellStartDate) SellStartYear, DATENAME(mm,SellStartDate) SellStartMonth,

       DAY(SellStartDate) SellStartDay, DATENAME(dw, SellStartDate) SellStartWeekday,

       ProductID, Name

FROM SalesLT.Product

ORDER BY SellStartYear;

  

SELECT DATEDIFF(yy,SellStartDate, GETDATE()) YearsSold, ProductID, Name

FROM SalesLT.Product

ORDER BY ProductID;

  

SELECT UPPER(Name) AS ProductName

FROM SalesLT.Product;

  

SELECT CONCAT(FirstName + ' ', LastName) AS FullName

FROM SalesLT.Customer;

  

SELECT Name, ProductNumber, LEFT(ProductNumber, 2) AS ProductType

FROM SalesLT.Product;

  

SELECT Name, ProductNumber, LEFT(ProductNumber, 2) AS ProductType,

                            SUBSTRING(ProductNumber,CHARINDEX('-', ProductNumber) + 1, 4) AS ModelCode,

                            SUBSTRING(ProductNumber, LEN(ProductNumber) - CHARINDEX('-', REVERSE(RIGHT(ProductNumber, 3))) + 2, 2) AS SizeCode

FROM SalesLT.Product;

  
  

-- Logical functions

SELECT Name, Size AS NumericSize

FROM SalesLT.Product

WHERE ISNUMERIC(Size) = 1;

  

SELECT Name, IIF(ProductCategoryID IN (5,6,7), 'Bike', 'Other') ProductType

FROM SalesLT.Product;

  

SELECT Name, IIF(ISNUMERIC(Size) = 1, 'Numeric', 'Non-Numeric') SizeType

FROM SalesLT.Product;

  

SELECT prd.Name AS ProductName, cat.Name AS Category,

      CHOOSE (cat.ParentProductCategoryID, 'Bikes','Components','Clothing','Accessories') AS ProductType

FROM SalesLT.Product AS prd

JOIN SalesLT.ProductCategory AS cat

ON prd.ProductCategoryID = cat.ProductCategoryID;

  
  

-- Window functions

SELECT TOP(100) ProductID, Name, ListPrice,

    RANK() OVER(ORDER BY ListPrice DESC) AS RankByPrice

FROM SalesLT.Product AS p

ORDER BY RankByPrice;

  

SELECT c.Name AS Category, p.Name AS Product, ListPrice,

    RANK() OVER(PARTITION BY c.Name ORDER BY ListPrice DESC) AS RankByPrice

FROM SalesLT.Product AS p

JOIN SalesLT.ProductCategory AS c

ON p.ProductCategoryID = c.ProductcategoryID

ORDER BY Category, RankByPrice;

  
  

-- Aggregate Functions

SELECT COUNT(*) AS Products, COUNT(DISTINCT ProductCategoryID) AS Categories, AVG(ListPrice) AS AveragePrice

FROM SalesLT.Product;

  

SELECT COUNT(p.ProductID) BikeModels, AVG(p.ListPrice) AveragePrice

FROM SalesLT.Product AS p

JOIN SalesLT.ProductCategory AS c

ON p.ProductCategoryID = c.ProductCategoryID

WHERE c.Name LIKE '%Bikes';

--#############

  

SELECT Salesperson, COUNT(CustomerID) Customers

FROM SalesLT.Customer

GROUP BY Salesperson

ORDER BY Salesperson;

  

SELECT c.Name AS Category, COUNT(p.ProductID) AS Products

FROM SalesLT.Product AS p

JOIN SalesLT.ProductCategory AS c

ON p.ProductCategoryID = c.ProductCategoryID

GROUP BY c.Name

ORDER BY Category;

  

SELECT c.Salesperson, SUM(oh.SubTotal) SalesRevenue

FROM SalesLT.Customer c

JOIN SalesLT.SalesOrderHeader oh

ON c.CustomerID = oh.CustomerID

GROUP BY c.Salesperson

ORDER BY SalesRevenue DESC;

  

SELECT c.Salesperson, ISNULL(SUM(oh.SubTotal), 0.00) SalesRevenue

FROM SalesLT.Customer c

LEFT JOIN SalesLT.SalesOrderHeader oh

ON c.CustomerID = oh.CustomerID

GROUP BY c.Salesperson

ORDER BY SalesRevenue DESC;

  

SELECT c.Salesperson, CONCAT(c.FirstName +' ', c.LastName) AS Customer, ISNULL(SUM(oh.SubTotal), 0.00) SalesRevenue

FROM SalesLT.Customer c

LEFT JOIN SalesLT.SalesOrderHeader oh

ON c.CustomerID = oh.CustomerID

GROUP BY c.Salesperson, CONCAT(c.FirstName +' ', c.LastName)

ORDER BY SalesRevenue DESC, Customer;

  

--##########

  

-- Try to find salespeople with over 150 customers (fails with error)

SELECT Salesperson, COUNT(CustomerID) Customers

FROM SalesLT.Customer

WHERE COUNT(CustomerID) > 100

GROUP BY Salesperson

ORDER BY Salesperson;

  

--Need to use HAVING clause to filter based on aggregate

SELECT Salesperson, COUNT(CustomerID) Customers

FROM SalesLT.Customer

GROUP BY Salesperson

HAVING COUNT(CustomerID) > 100

ORDER BY Salesperson;

  

-- #######################    MODEL SIX  ############################################

  

--Display a list of products whose list price is higher than the highest unit price of items that have sold

  

SELECT MAX(UnitPrice) FROM SalesLT.SalesOrderDetail

  

SELECT * from SalesLT.Product

WHERE ListPrice >

  
  

SELECT * from SalesLT.Product

WHERE ListPrice >

(SELECT MAX(UnitPrice) FROM SalesLT.SalesOrderDetail)

--#################

  

--List products that have an order quantity greater than 20

  

SELECT Name FROM SalesLT.Product

WHERE ProductID IN

(SELECT ProductID from SalesLT.SalesOrderDetail

WHERE OrderQty>20)

  

SELECT Name

FROM SalesLT.Product P

JOIN SalesLT.SalesOrderDetail SOD

ON P.ProductID=SOD.ProductID

WHERE OrderQty>20

  

--##########3

  

--For each customer list all sales on the last day that they made a sale

  

SELECT CustomerID, SalesOrderID, OrderDate

FROM SalesLT.SalesOrderHeader AS SO1

ORDER BY CustomerID,OrderDate

  

SELECT CustomerID, SalesOrderID, OrderDate

FROM SalesLT.SalesOrderHeader AS SO1

WHERE orderdate =

(SELECT MAX(orderdate)

FROM SalesLT.SalesOrderHeader)

  
  

SELECT CustomerID, SalesOrderID, OrderDate

FROM SalesLT.SalesOrderHeader AS SO1

WHERE orderdate =

(SELECT MAX(orderdate)

FROM SalesLT.SalesOrderHeader AS SO2

WHERE SO2.CustomerID = SO1.CustomerID)

ORDER BY CustomerID

  

--############

  
  

-- Setup

CREATE FUNCTION SalesLT.udfMaxUnitPrice (@SalesOrderID int)

RETURNS TABLE

AS

RETURN

SELECT SalesOrderID,Max(UnitPrice) as MaxUnitPrice FROM

SalesLT.SalesOrderDetail

WHERE SalesOrderID=@SalesOrderID

GROUP BY SalesOrderID;

  

--Display the sales order details for items that are equal to

-- the maximum unit price for that sales order

SELECT * FROM SalesLT.SalesOrderDetail AS SOH

CROSS APPLY SalesLT.udfMaxUnitPrice(SOH.SalesOrderID) AS MUP

WHERE SOH.UnitPrice=MUP.MaxUnitPrice

ORDER BY SOH.SalesOrderID;

  
  

-- #######################    MODEL SEVEN  ############################################

  

-- Create a view

CREATE VIEW SalesLT.vCustomerAddress

AS

SELECT C.CustomerID, FirstName, LastName, AddressLine1, City, StateProvince

FROM

SalesLT.Customer C JOIN SalesLT.CustomerAddress CA

ON C.CustomerID=CA.CustomerID

JOIN SalesLT.Address A

ON CA.AddressID=A.AddressID

  

-- Query the view

SELECT CustomerID, City

FROM SalesLT.vCustomerAddress

  

-- Join the view to a table

SELECT c.StateProvince, c.City, ISNULL(SUM(s.TotalDue), 0.00) AS Revenue

FROM SalesLT.vCustomerAddress AS c

LEFT JOIN SalesLT.SalesOrderHeader AS s

ON s.CustomerID = c.CustomerID

GROUP BY c.StateProvince, c.City

ORDER BY c.StateProvince, Revenue DESC;

  

--#################

-- Temporary table

CREATE TABLE #Colors

(Color varchar(15));

  

INSERT INTO #Colors

SELECT DISTINCT Color FROM SalesLT.Product;

  

SELECT * FROM #Colors;

  

-- Table variable

DECLARE @Colors AS TABLE (Color varchar(15));

  

INSERT INTO @Colors

SELECT DISTINCT Color FROM SalesLT.Product;

  

SELECT * FROM @Colors;

  

-- New batch

SELECT * FROM #Colors;

  

SELECT * FROM @Colors; -- now out of scope

--###################

CREATE FUNCTION SalesLT.udfCustomersByCity

(@City AS VARCHAR(20))

RETURNS TABLE

AS

RETURN

(SELECT C.CustomerID, FirstName, LastName, AddressLine1, City, StateProvince

 FROM SalesLT.Customer C JOIN SalesLT.CustomerAddress CA

 ON C.CustomerID=CA.CustomerID

 JOIN SalesLT.Address A ON CA.AddressID=A.AddressID

 WHERE City=@City);

  
  

SELECT * FROM SalesLT.udfCustomersByCity('Bellevue')

--################

  

SELECT Category, COUNT(ProductID) AS Products

FROM

    (SELECT p.ProductID, p.Name AS Product, c.Name AS Category

     FROM SalesLT.Product AS p

     JOIN SalesLT.ProductCategory AS c

     ON p.ProductCategoryID = c.ProductCategoryID) AS ProdCats

GROUP BY Category

ORDER BY Category;

--############3

  

--Using a CTE

WITH ProductsByCategory (ProductID, ProductName, Category)

AS

(

    SELECT p.ProductID, p.Name, c.Name AS Category

     FROM SalesLT.Product AS p

     JOIN SalesLT.ProductCategory AS c

     ON p.ProductCategoryID = c.ProductCategoryID

)

  

SELECT Category, COUNT(ProductID) AS Products

FROM ProductsByCategory

GROUP BY Category

ORDER BY Category;

  
  

-- Recursive CTE

SELECT * FROM SalesLT.Employee

  

-- Using the CTE to perform recursion

WITH OrgReport (ManagerID, EmployeeID, EmployeeName, Level)

AS

(

    -- Anchor query

    SELECT e.ManagerID, e.EmployeeID, EmployeeName, 0

    FROM SalesLT.Employee AS e

    WHERE ManagerID IS NULL

  

    UNION ALL

  

    -- Recursive query

    SELECT e.ManagerID, e.EmployeeID, e.EmployeeName, Level + 1

    FROM SalesLT.Employee AS e

    INNER JOIN OrgReport AS o ON e.ManagerID = o.EmployeeID

)

  

SELECT * FROM OrgReport

OPTION (MAXRECURSION 3);

  

-- #######################    MODEL EIGHT  ############################################

  

SELECT cat.ParentProductCategoryName, cat.ProductCategoryName, count(prd.ProductID) AS Products

FROM SalesLT.vGetAllCategories as cat

LEFT JOIN SalesLT.Product AS prd

ON prd.ProductCategoryID = cat.ProductcategoryID

GROUP BY cat.ParentProductCategoryName, cat.ProductCategoryName

--GROUP BY GROUPING SETS(cat.ParentProductCategoryName, cat.ProductCategoryName, ())

--GROUP BY ROLLUP (cat.ParentProductCategoryName, cat.ProductCategoryName)

--GROUP BY CUBE (cat.ParentProductCategoryName, cat.ProductCategoryName)

ORDER BY cat.ParentProductCategoryName, cat.ProductCategoryName;

--##############3

  

SELECT * FROM

(SELECT P.ProductID, PC.Name,ISNULL(P.Color, 'Uncolored') AS Color

 FROM saleslt.productcategory AS PC

 JOIN SalesLT.Product AS P

 ON PC.ProductCategoryID=P.ProductCategoryID

 ) AS PPC

PIVOT(COUNT(ProductID) FOR Color IN([Red],[Blue],[Black],[Silver],[Yellow],[Grey], [Multi], [Uncolored])) as pvt

ORDER BY Name;

  

-- Unpivot

CREATE TABLE #ProductColorPivot

(Name varchar(50), Red int, Blue int, Black int, Silver int, Yellow int, Grey int , multi int, uncolored int);

  

INSERT INTO #ProductColorPivot

SELECT * FROM

(SELECT P.ProductID, PC.Name,ISNULL(P.Color, 'Uncolored') AS Color

 FROM saleslt.productcategory AS PC

 JOIN SalesLT.Product AS P

 ON PC.ProductCategoryID=P.ProductCategoryID

 ) AS PPC

PIVOT(COUNT(ProductID) FOR Color IN([Red],[Blue],[Black],[Silver],[Yellow],[Grey], [Multi], [Uncolored])) as pvt

ORDER BY Name;

  

SELECT Name, Color, ProductCount

FROM

(SELECT Name,

[Red],[Blue],[Black],[Silver],[Yellow],[Grey], [Multi], [Uncolored]

FROM #ProductColorPivot) pcp

UNPIVOT

(ProductCount FOR Color IN ([Red],[Blue],[Black],[Silver],[Yellow],[Grey], [Multi], [Uncolored])

) AS ProductCounts

  
  

-- #######################    MODEL NINE  ############################################

  

-- Create a table for the demo

CREATE TABLE SalesLT.CallLog

(

    CallID int IDENTITY PRIMARY KEY NOT NULL,

    CallTime datetime NOT NULL DEFAULT GETDATE(),

    SalesPerson nvarchar(256) NOT NULL,

    CustomerID int NOT NULL REFERENCES SalesLT.Customer(CustomerID),

    PhoneNumber nvarchar(25) NOT NULL,

    Notes nvarchar(max) NULL

);

GO

  

-- Insert a row

INSERT INTO SalesLT.CallLog

VALUES

('2015-01-01T12:30:00', 'adventure-works\pamela0', 1, '245-555-0173', 'Returning call re: enquiry about delivery');

  

SELECT * FROM SalesLT.CallLog;

  

-- Insert defaults and nulls

INSERT INTO SalesLT.CallLog

VALUES

(DEFAULT, 'adventure-works\david8', 2, '170-555-0127', NULL);

  

SELECT * FROM SalesLT.CallLog;

  

-- Insert a row with explicit columns

INSERT INTO SalesLT.CallLog (SalesPerson, CustomerID, PhoneNumber)

VALUES

('adventure-works\jillian0', 3, '279-555-0130');

  

SELECT * FROM SalesLT.CallLog;

  

-- Insert multiple rows

INSERT INTO SalesLT.CallLog

VALUES

(DATEADD(mi,-2, GETDATE()), 'adventure-works\jillian0', 4, '710-555-0173', NULL),

(DEFAULT, 'adventure-works\shu0', 5, '828-555-0186', 'Called to arrange deliver of order 10987');

  

SELECT * FROM SalesLT.CallLog;

  

-- Insert the results of a query

INSERT INTO SalesLT.CallLog (SalesPerson, CustomerID, PhoneNumber, Notes)

SELECT SalesPerson, CustomerID, Phone, 'Sales promotion call'

FROM SalesLT.Customer

WHERE CompanyName = 'Big-Time Bike Store';

  

SELECT * FROM SalesLT.CallLog;

  

-- Retrieving inserted identity

INSERT INTO SalesLT.CallLog (SalesPerson, CustomerID, PhoneNumber)

VALUES

('adventure-works\jos�1', 10, '150-555-0127');

  

SELECT SCOPE_IDENTITY();

  

SELECT * FROM SalesLT.CallLog;

  

--Overriding Identity

SET IDENTITY_INSERT SalesLT.CallLog ON;

  

INSERT INTO SalesLT.CallLog (CallID, SalesPerson, CustomerID, PhoneNumber)

VALUES

(9, 'adventure-works\jos�1', 11, '926-555-0159');

  

SET IDENTITY_INSERT SalesLT.CallLog OFF;

  

SELECT * FROM SalesLT.CallLog;

  

--###############3

  

-- Update a table

UPDATE SalesLT.CallLog

SET Notes = 'No notes'

WHERE Notes IS NULL;

  

SELECT * FROM SalesLT.CallLog;

  

-- Update multiple columns

UPDATE SalesLT.CallLog

SET SalesPerson = '', PhoneNumber = ''

  

SELECT * FROM SalesLT.CallLog;

  

-- Update from results of a query

UPDATE SalesLT.CallLog

SET SalesPerson = c.SalesPerson, PhoneNumber = c.Phone

FROM SalesLT.Customer AS c

WHERE c.CustomerID = SalesLT.CallLog.CustomerID;

  

SELECT * FROM SalesLT.CallLog;

  

-- Delete rows

DELETE FROM SalesLT.CallLog

WHERE CallTime < DATEADD(dd, -7, GETDATE());

  

SELECT * FROM SalesLT.CallLog;

  

-- Truncate the table

TRUNCATE TABLE SalesLT.CallLog;

  

SELECT * FROM SalesLT.CallLog;

  

-- #######################    MODEL TEN  ############################################

--###  SETUP

IF OBJECT_ID('SalesLT.DemoTable') IS NOT NULL

    BEGIN

    DROP TABLE SalesLT.DemoTable

    END

GO

  
  

CREATE TABLE SalesLT.DemoTable

(ID INT IDENTITY(1,1),

Description Varchar(20),

CONSTRAINT [PK_DemoTable] PRIMARY KEY CLUSTERED(ID)

)

GO

--########### VARIBALE

  

--Search by city using a variable

DECLARE @City VARCHAR(20)='Toronto'

Set @City='Bellevue'

  
  
  

Select FirstName +' '+LastName as [Name],AddressLine1 as Address,City

FROM SalesLT.Customer as C

JOIN SalesLT.CustomerAddress as CA

ON C.CustomerID=CA.CustomerID

JOIN SalesLT.Address as A

ON CA.AddressID=A.AddressID

WHERE City=@City

  

--Use a variable as an output

DECLARE @Result money

SELECT @Result=MAX(TotalDue)

FROM SalesLT.SalesOrderHeader

  

PRINT @Result

---###################################

  

--Simple logical test

If 'Yes'='Yes'

Print 'True'

  

--Change code based on a condition

UPDATE SalesLT.Product

SET DiscontinuedDate=getdate()

WHERE ProductID=1;

  

IF @@ROWCOUNT<1

BEGIN

    PRINT 'Product was not found'

END

ELSE

BEGIN

    PRINT 'Product Updated'

END

--#############3

  

-- WHILE LIST

DECLARE @Counter int=1

  

WHILE @Counter <=5

  

BEGIN

    INSERT SalesLT.DemoTable(Description)

    VALUES ('ROW '+CONVERT(varchar(5),@Counter))

    SET @Counter=@Counter+1

END

  

SELECT Description FROM SalesLT.DemoTable

  
  

--Testing for existing values

DECLARE @Counter int=1

  

DECLARE @Description int

SELECT @Description=MAX(ID)

FROM SalesLT.DemoTable

  

WHILE @Counter <5

BEGIN

    INSERT SalesLT.DemoTable(Description)

    VALUES ('ROW '+CONVERT(varchar(5),@Description))

    SET @Description=@Description+1

    SET @Counter=@Counter+1

END

  

SELECT Description FROM SalesLT.DemoTable

  

--###########

  

-- Create a stored procedure

CREATE PROCEDURE SalesLT.GetProductsByCategory (@CategoryID INT = NULL)

AS

IF @CategoryID IS NULL

    SELECT ProductID, Name, Color, Size, ListPrice

    FROM SalesLT.Product

ELSE

    SELECT ProductID, Name, Color, Size, ListPrice

    FROM SalesLT.Product

    WHERE ProductCategoryID = @CategoryID;

  
  

-- Execute the procedure without a parameter

EXEC SalesLT.GetProductsByCategory

  

-- Execute the procedure with a parameter

EXEC SalesLT.GetProductsByCategory 6

  
  

-- ########    MODEL ELEVEN  ##########################

  

-- View a system error

INSERT INTO SalesLT.SalesOrderDetail (SalesOrderID, OrderQty, ProductID, UnitPrice, UnitPriceDiscount)

VALUES

(100000, 1, 680, 1431.50, 0.00);

  
  
  
  
  

-- Raise an error with RAISERROR

UPDATE SalesLT.Product

SET DiscontinuedDate = GETDATE()

WHERE ProductID = 0;

IF @@ROWCOUNT < 1

    RAISERROR('The product was not found - no products have been updated', 16, 0);

-- Raise an error with THROW

UPDATE SalesLT.Product

SET DiscontinuedDate = GETDATE()

WHERE ProductID = 0;

  

IF @@ROWCOUNT < 1

    THROW 50001, 'The product was not found - no products have been updated', 0;

  

    --#################

  

    -- catch an error

BEGIN TRY

    UPDATE SalesLT.Product

    SET ProductNumber = ProductID / ISNULL(Weight, 0);

END TRY

BEGIN CATCH

    PRINT 'The following error occurred:';

    PRINT ERROR_MESSAGE();

END CATCH;

  

-- Catch and rethrow

BEGIN TRY

    UPDATE SalesLT.Product

    SET ProductNumber = ProductID / ISNULL(Weight, 0);

END TRY

BEGIN CATCH

    PRINT 'The following error occurred:';

    PRINT ERROR_MESSAGE();

    THROW;

END CATCH;

  

-- Catch, log, and throw a custom error

BEGIN TRY

    UPDATE SalesLT.Product

    SET ProductNumber = ProductID / ISNULL(Weight, 0);

END TRY

BEGIN CATCH

    DECLARE @ErrorLogID as int, @ErrorMsg AS varchar(250);

    EXECUTE dbo.uspLogError @ErrorLogID OUTPUT;

    SET @ErrorMsg = 'The update failed because of an error. View error #'

                     + CAST(@ErrorLogID AS varchar)

                     + ' in the error log for details.';

    THROW 50001, @ErrorMsg, 0;

END CATCH;

  

-- View the error log

SELECT * FROM dbo.ErrorLog;

  

--######################

  

-- No transaction

BEGIN TRY

    INSERT INTO SalesLT.SalesOrderHeader (DueDate, CustomerID, ShipMethod)

    VALUES

    (DATEADD(dd, 7, GETDATE()), 1, 'STD DELIVERY');

  

    DECLARE @SalesOrderID int = SCOPE_IDENTITY();

  

    INSERT INTO SalesLT.SalesOrderDetail (SalesOrderID, OrderQty, ProductID, UnitPrice, UnitPriceDiscount)

    VALUES

    (@SalesOrderID, 1, 99999, 1431.50, 0.00);

END TRY

BEGIN CATCH

    PRINT ERROR_MESSAGE();

END CATCH;

  

-- View orphaned orders

SELECT h.SalesOrderID, h.DueDate, h.CustomerID, h.ShipMethod, d.SalesOrderDetailID

FROM SalesLT.SalesOrderHeader AS h

LEFT JOIN SalesLT.SalesOrderDetail AS d

ON d.SalesOrderID = h.SalesOrderID

WHERE D.SalesOrderDetailID IS NULL;

  

-- Manually delete orphaned record

DELETE FROM SalesLT.SalesOrderHeader

WHERE SalesOrderID = SCOPE_IDENTITY();

  

-- Use a transaction

BEGIN TRY

  BEGIN TRANSACTION

    INSERT INTO SalesLT.SalesOrderHeader (DueDate, CustomerID, ShipMethod)

    VALUES

    (DATEADD(dd, 7, GETDATE()), 1, 'STD DELIVERY');

  

    DECLARE @SalesOrderID int = SCOPE_IDENTITY();

  

    INSERT INTO SalesLT.SalesOrderDetail (SalesOrderID, OrderQty, ProductID, UnitPrice, UnitPriceDiscount)

    VALUES

    (@SalesOrderID, 1, 99999, 1431.50, 0.00);

  COMMIT TRANSACTION

END TRY

BEGIN CATCH

  IF @@TRANCOUNT > 0

  BEGIN

    PRINT XACT_STATE();

    ROLLBACK TRANSACTION;

  END

  PRINT ERROR_MESSAGE();

  THROW 50001,'An insert failed. The transaction was cancelled.', 0;

END CATCH;

  

-- Check for orphaned orders

SELECT h.SalesOrderID, h.DueDate, h.CustomerID, h.ShipMethod, d.SalesOrderDetailID

FROM SalesLT.SalesOrderHeader AS h

LEFT JOIN SalesLT.SalesOrderDetail AS d

ON d.SalesOrderID = h.SalesOrderID

WHERE D.SalesOrderDetailID IS NULL

  

-- Use XACT_ABORT

SET XACT_ABORT ON;

BEGIN TRY

  BEGIN TRANSACTION

    INSERT INTO SalesLT.SalesOrderHeader (DueDate, CustomerID, ShipMethod)

    VALUES

    (DATEADD(dd, 7, GETDATE()), 1, 'STD DELIVERY');

  

    DECLARE @SalesOrderID int = SCOPE_IDENTITY();

  

    INSERT INTO SalesLT.SalesOrderDetail (SalesOrderID, OrderQty, ProductID, UnitPrice, UnitPriceDiscount)

    VALUES

    (@SalesOrderID, 1, 99999, 1431.50, 0.00);

  COMMIT TRANSACTION

END TRY

BEGIN CATCH

  PRINT ERROR_MESSAGE();

  THROW 50001,'An insert failed. The transaction was cancelled.', 0;

END CATCH;

SET XACT_ABORT OFF;

  

-- Check for orphaned orders

SELECT h.SalesOrderID, h.DueDate, h.CustomerID, h.ShipMethod, d.SalesOrderDetailID

FROM SalesLT.SalesOrderHeader AS h

LEFT JOIN SalesLT.SalesOrderDetail AS d

ON d.SalesOrderID = h.SalesOrderID

WHERE D.SalesOrderDetailID IS NULL

``` 
