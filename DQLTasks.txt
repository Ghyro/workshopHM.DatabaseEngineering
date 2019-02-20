/* TASK 1*/

SELECT c.CompanyName FROM Customers c
	INNER JOIN Orders o ON c.CustomerID = o.CustomerID
	INNER JOIN [Order Details] od ON o.OrderID = od.OrderID
	GROUP BY c.CompanyName
	HAVING SUM(Quantity	* UnitPrice) > 1000

/* TASK 2*/

-- without group by
SELECT Suppliers.CompanyName AS Supplier, Customers.CompanyName AS Company, Customers.City AS City FROM Customers
	INNER JOIN Suppliers ON Suppliers.City = Customers.City
	AND Suppliers.Country = Customers.Country
	ORDER BY Suppliers.CompanyName

-- with group by	
SELECT Suppliers.CompanyName AS Supplier, Customers.CompanyName AS Company, Customers.City AS City FROM Customers
	INNER JOIN Suppliers ON Suppliers.City = Customers.City
	AND Suppliers.Country = Customers.Country
	GROUP BY Suppliers.CompanyName, Customers.CompanyName, Customers.City
	ORDER BY Suppliers.CompanyName

/* TASK 3*/

SELECT c.CompanyName, MAX(od.Quantity * od.UnitPrice) as MaxSumOrder FROM Customers as c
	INNER JOIN Orders AS o ON c.CustomerID = o.CustomerID
	INNER JOIN [Order Details] AS od ON o.OrderID = od.OrderID
	GROUP BY c.CompanyName
	HAVING MAX(od.Quantity * od.UnitPrice) > 1000
	ORDER BY MAX(od.Quantity * od.UnitPrice)

/* TASK 4*/

SELECT c.CustomerID, c.CompanyName, YEAR(MIN(o.OrderDate)) AS FirstYear, MONTH(MIN(o.OrderDate)) AS FirstMonth 
FROM Customers AS c
	INNER JOIN Orders AS o ON c.CustomerID = o.CustomerID
	GROUP BY c.CustomerID, c.CompanyName

/* TASK 5*/

SELECT c.CustomerID, c.CompanyName, YEAR(MIN(o.OrderDate)) AS FirstYear, MONTH(MIN(o.OrderDate)) AS FirstMonth, SUM(od.Quantity * od.UnitPrice) AS Sum
FROM Customers AS c
	INNER JOIN Orders AS o ON c.CustomerID = o.CustomerID
	INNER JOIN [Order Details] AS od ON o.OrderID = od.OrderID
	GROUP BY c.CustomerID, c.CompanyName
	ORDER BY FirstYear, FirstMonth, Sum DESC, c.CompanyName

/* TASK 6*/

SELECT c.CustomerID, c.CompanyName, c.PostalCode, c.Region, c.Phone FROM Customers AS c
	WHERE ISNUMERIC(c.PostalCode) = 0 OR c.Region IS NULL OR c.Phone NOT LIKE '(%'

/* TASK 7*/

SELECT cat.CategoryName, CASE WHEN p.UnitsInStock > 0 Then 'Available'
							  WHEN p.UnitsInStock = 0 Then 'Not available'
						 END AS inStock,
						 SUM(p.UnitPrice * p.UnitsInStock) AS CostOf
FROM Categories AS cat
	INNER JOIN Products AS p ON cat.CategoryID = p.CategoryID
	GROUP BY cat.CategoryName, CASE
								WHEN p.UnitsInStock > 0 Then 'Available'
								WHEN p.UnitsInStock = 0 Then 'Not available'
							   END
	ORDER BY CostOf

/* TASK 8*/

-- The first variant
DECLARE @cheap int, @average int, @expensive int;
SET @cheap = 10;
SET @expensive = 50;

SELECT ProductName, CASE WHEN UnitPrice <= @cheap THEN 'Cheap'
						 WHEN UnitPrice > @cheap AND UnitPrice < @expensive THEN 'Average'
						 WHEN UnitPrice >= @expensive THEN 'Expensive'
					END AS GroupOf
FROM Products
GROUP BY ProductName, CASE WHEN UnitPrice <= @cheap THEN 'Cheap'
						   WHEN UnitPrice > @cheap AND UnitPrice < @expensive THEN 'Average'
						   WHEN UnitPrice >= @expensive THEN 'Expensive'
					  END
-- The second variant
SELECT CASE WHEN UnitPrice <= 10 THEN 'Cheap'
			WHEN UnitPrice > 10 AND UnitPrice < 50 THEN 'Average'
			WHEN UnitPrice >= 10 THEN 'Expensive'
	   END AS Category,
	   COUNT(Products.UnitPrice) AS CountOf
FROM Products
GROUP BY CASE WHEN UnitPrice <= 10 THEN 'Cheap'
			  WHEN UnitPrice > 10 AND UnitPrice < 50 THEN 'Average'
			  WHEN UnitPrice >= 10 THEN 'Expensive'
		 END

/* TASK 9*/

SELECT c.City, AVG(od.UnitPrice * od.Quantity) AS AverageProfit,
			   COUNT(o.OrderID)/COUNT(distinct o.CustomerID) AS AverageIntensity
FROM Customers AS c
INNER JOIN Orders AS o ON c.CustomerID = o.CustomerID
INNER JOIN [Order Details] AS od ON o.OrderID = od.OrderID
GROUP BY c.City

/* TASK 10*/

-- Monthly statistics
SELECT DATENAME(MONTH, o.OrderDate) AS [Month], COUNT(o.OrderID) AS NumberOfOrders, SUM(od.Quantity * od.UnitPrice) AS TotalSum
FROM Orders AS o
INNER JOIN [Order Details] AS od ON o.OrderID = od.OrderID
GROUP BY MONTH(o.OrderDate), DATENAME(MONTH, o.OrderDate)
ORDER BY MONTH(o.OrderDate)

-- Annual statistics
SELECT DATENAME(YEAR, o.OrderDate) AS [Year], COUNT(o.OrderID) AS NumberOfOrders, SUM(od.Quantity * od.UnitPrice) AS TotalSum
FROM Orders AS o
INNER JOIN [Order Details] AS od ON o.OrderID = od.OrderID
GROUP BY YEAR(o.OrderDate), DATENAME(YEAR, o.OrderDate)
ORDER BY YEAR(o.OrderDate)

-- Statistics for the year - month
SELECT DATENAME(YEAR, o.OrderDate) AS [Year], DATENAME(MONTH, o.OrderDate) AS [Month], COUNT(o.OrderID) AS NumberOfOrders, SUM(od.Quantity * od.UnitPrice) AS TotalSum
FROM Orders AS o
INNER JOIN [Order Details] AS od ON o.OrderID = od.OrderID
GROUP BY YEAR(o.OrderDate),MONTH(o.OrderDate), DATENAME(MONTH, o.OrderDate), DATENAME(YEAR, o.OrderDate)
ORDER BY YEAR(o.OrderDate), MONTH(o.OrderDate)