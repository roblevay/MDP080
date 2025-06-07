## Lab 4

**Use Built-in Functions**
In this exercise, you'll use built-in functions to retrieve and aggregate data in the Adventureworks database.

> **Note**: This exercise assumes you have created the sample AdventureWorks database.

---

### Scalar functions

Open a query editor for your AdventureWorks database, and create a new query.

```sql
SELECT YEAR(SellStartDate) AS SellStartYear, ProductID, Name
FROM SalesLT.Product
ORDER BY SellStartYear;
```

```sql
SELECT YEAR(SellStartDate) AS SellStartYear,
       DATENAME(mm,SellStartDate) AS SellStartMonth,
       DAY(SellStartDate) AS SellStartDay,
       DATENAME(dw, SellStartDate) AS SellStartWeekday,
       DATEDIFF(yy,SellStartDate, GETDATE()) AS YearsSold,
       ProductID,
       Name
FROM SalesLT.Product
ORDER BY SellStartYear;
```

```sql
SELECT CONCAT(FirstName + ' ', LastName) AS FullName
FROM SalesLT.Customer;
```

```sql
SELECT UPPER(Name) AS ProductName,
       ProductNumber,
       ROUND(Weight, 0) AS ApproxWeight,
       LEFT(ProductNumber, 2) AS ProductType,
       SUBSTRING(ProductNumber,CHARINDEX('-', ProductNumber) + 1, 4) AS ModelCode,
       SUBSTRING(ProductNumber, LEN(ProductNumber) - CHARINDEX('-', REVERSE(RIGHT(ProductNumber, 3))) + 2, 2) AS SizeCode
FROM SalesLT.Product;
```

---

### Logical functions

```sql
SELECT Name, Size AS NumericSize
FROM SalesLT.Product
WHERE ISNUMERIC(Size) = 1;
```

```sql
SELECT Name, IIF(ISNUMERIC(Size) = 1, 'Numeric', 'Non-Numeric') AS SizeType
FROM SalesLT.Product;
```

```sql
SELECT prd.Name AS ProductName,
       cat.Name AS Category,
       CHOOSE (cat.ParentProductCategoryID, 'Bikes','Components','Clothing','Accessories') AS ProductType
FROM SalesLT.Product AS prd
JOIN SalesLT.ProductCategory AS cat
    ON prd.ProductCategoryID = cat.ProductCategoryID;
```

---

### Aggregate functions

```sql
SELECT COUNT(*) AS Products,
       COUNT(DISTINCT ProductCategoryID) AS Categories,
       AVG(ListPrice) AS AveragePrice
FROM SalesLT.Product;
```

```sql
SELECT COUNT(p.ProductID) AS BikeModels, AVG(p.ListPrice) AS AveragePrice
FROM SalesLT.Product AS p
JOIN SalesLT.ProductCategory AS c
    ON p.ProductCategoryID = c.ProductCategoryID
WHERE c.Name LIKE '%Bikes';
```

---

### Group aggregated results with the GROUP BY clause

```sql
SELECT Salesperson, COUNT(CustomerID) AS Customers
FROM SalesLT.Customer
GROUP BY Salesperson
ORDER BY Salesperson;
```

```sql
SELECT c.Salesperson, SUM(oh.SubTotal) AS SalesRevenue
FROM SalesLT.Customer c
JOIN SalesLT.SalesOrderHeader oh
    ON c.CustomerID = oh.CustomerID
GROUP BY c.Salesperson
ORDER BY SalesRevenue DESC;
```

```sql
SELECT c.Salesperson, ISNULL(SUM(oh.SubTotal), 0.00) AS SalesRevenue
FROM SalesLT.Customer c
LEFT JOIN SalesLT.SalesOrderHeader oh
    ON c.CustomerID = oh.CustomerID
GROUP BY c.Salesperson
ORDER BY SalesRevenue DESC;
```

---

### Filter groups with the HAVING clause

```sql
SELECT Salesperson, COUNT(CustomerID) AS Customers
FROM SalesLT.Customer
GROUP BY Salesperson
HAVING COUNT(CustomerID) > 100
ORDER BY Salesperson;
```

---

## Challenges

> **Tip**: Try to determine the appropriate queries for yourself. If you get stuck, suggested answers are provided at the end of this lab.

### Challenge 1: Retrieve order shipping information

```sql
SELECT SalesOrderID,
       ROUND(Freight, 2) AS FreightCost,
       LOWER(ShipMethod) AS ShippingMethod,
       YEAR(ShipDate) AS ShipYear,
       DATENAME(mm, ShipDate) AS ShipMonth,
       DAY(ShipDate) AS ShipDay
FROM SalesLT.SalesOrderHeader;
```

### Challenge 2: Aggregate product sales

```sql
SELECT p.Name, SUM(o.OrderQty) AS TotalSales
FROM SalesLT.SalesOrderDetail AS o
JOIN SalesLT.Product AS p
    ON o.ProductID = p.ProductID
WHERE p.ListPrice > 1000
GROUP BY p.Name
HAVING SUM(o.OrderQty) > 20
ORDER BY TotalSales DESC;
```

---

*End of Lab 4 Instructions, Challenges, and Solutions*

