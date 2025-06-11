# Lab 4

## Use Built-in Functions

In this exercise, you'll use built-in functions to retrieve and aggregate data in the AdventureWorks database.

> **Note**: This exercise assumes you have created the sample AdventureWorks database.

---

### Scalar functions

Transact-SQL provides a large number of functions that you can use to extract additional information from your data. Most of these functions are scalar functions that return a single value based on one or more input parameters, often a data field.

> **Tip**: We don't have enough time in this exercise to explore every function available in Transact-SQL. To learn more about the functions covered in this exercise, and more, view the Transact-SQL documentation.

Open a query editor for your AdventureWorks database, and create a new query.

In the query editor, enter the following code:

```sql
SELECT YEAR(SellStartDate) AS SellStartYear, ProductID, Name
FROM SalesLT.Product
ORDER BY SellStartYear;
```

Run the query, and after a few seconds, review the results, noting that the `YEAR` function has retrieved the year from the `SellStartDate` field.

Modify the query as follows to use some additional scalar functions that operate on datetime values:

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

Run the query and review the results.

Note that the `DATENAME` function returns a different value depending on the `datepart` parameter that is passed to it. In this example, `mm` returns the month name, and `dw` returns the weekday name.

Note also that the `DATEDIFF` function returns the specified time interval between a start date and an end date. In this case the interval is measured in years (`yy`), and the end date is determined by the `GETDATE` function, which when used with no parameters returns the current date and time.

Replace the existing query with the following code:

```sql
SELECT CONCAT(FirstName + ' ', LastName) AS FullName
FROM SalesLT.Customer;
```

Run the query and note that it returns the concatenated first and last name for each customer.

Replace the query with the following code to explore some more functions that manipulate string-based values:

```sql
SELECT UPPER(Name) AS ProductName,
       ProductNumber,
       ROUND(Weight, 0) AS ApproxWeight,
       LEFT(ProductNumber, 2) AS ProductType,
       SUBSTRING(ProductNumber,CHARINDEX('-', ProductNumber) + 1, 4) AS ModelCode,
       SUBSTRING(ProductNumber, LEN(ProductNumber) - CHARINDEX('-', REVERSE(RIGHT(ProductNumber, 3))) + 2, 2) AS SizeCode
FROM SalesLT.Product;
```

Run the query and note that it returns the following data:

* The product name, converted to upper case by the `UPPER` function.
* The product number, which is a string code that encapsulates details of the product.
* The weight of the product, rounded to the nearest whole number using the `ROUND` function.
* The product type, indicated by the first two characters of the product number, starting from the left (`LEFT` function).
* The model code, extracted using the `SUBSTRING` function and `CHARINDEX`.
* The size code, extracted using `SUBSTRING` and a combination of `LEN`, `CHARINDEX`, `REVERSE`, and `RIGHT`.

This example shows how you can combine functions to apply fairly complex logic to extract the values you need.

---

## Use logical functions

Logical functions are used to apply logical tests to values, and return an appropriate value based on the results of the logical evaluation.

Replace the existing query with the following code:

```sql
SELECT Name, Size AS NumericSize
FROM SalesLT.Product
WHERE ISNUMERIC(Size) = 1;
```

Run the query and note that the results only include products with a numeric size.

Replace the query with the following code:

```sql
SELECT Name, IIF(ISNUMERIC(Size) = 1, 'Numeric', 'Non-Numeric') AS SizeType
FROM SalesLT.Product;
```

Run the query and review the results.

Replace the query with the following code:

```sql
SELECT prd.Name AS ProductName,
       cat.Name AS Category,
       CHOOSE (cat.ParentProductCategoryID, 'Bikes','Components','Clothing','Accessories') AS ProductType
FROM SalesLT.Product AS prd
JOIN SalesLT.ProductCategory AS cat
    ON prd.ProductCategoryID = cat.ProductCategoryID;
```

Run the query and note that the `CHOOSE` function returns the value in the ordinal position in a list based on a specified index value. The list index is 1-based.

---

## Use aggregate functions

Aggregate functions return an aggregated value, such as a sum, count, average, minimum, or maximum.

Replace the existing query with the following code:

```sql
SELECT COUNT(*) AS Products,
       COUNT(DISTINCT ProductCategoryID) AS Categories,
       AVG(ListPrice) AS AveragePrice
FROM SalesLT.Product;
```

Run the query and note that the following aggregations are returned:

* The number of products (`COUNT(*)`).
* The number of distinct categories (`COUNT(DISTINCT ProductCategoryID)`).
* The average price (`AVG(ListPrice)`).

Replace the query with the following code:

```sql
SELECT COUNT(p.ProductID) AS BikeModels, AVG(p.ListPrice) AS AveragePrice
FROM SalesLT.Product AS p
JOIN SalesLT.ProductCategory AS c
    ON p.ProductCategoryID = c.ProductCategoryID
WHERE c.Name LIKE '%Bikes';
```

Run the query and note the result.

---

## Group aggregated results with the GROUP BY clause

Replace the existing query with the following code:

```sql
SELECT Salesperson, COUNT(CustomerID) AS Customers
FROM SalesLT.Customer
GROUP BY Salesperson
ORDER BY Salesperson;
```

Replace the query with the following code:

```sql
SELECT c.Salesperson, SUM(oh.SubTotal) AS SalesRevenue
FROM SalesLT.Customer c
JOIN SalesLT.SalesOrderHeader oh
    ON c.CustomerID = oh.CustomerID
GROUP BY c.Salesperson
ORDER BY SalesRevenue DESC;
```

Modify the query as follows to use an outer join:

```sql
SELECT c.Salesperson, ISNULL(SUM(oh.SubTotal), 0.00) AS SalesRevenue
FROM SalesLT.Customer c
LEFT JOIN SalesLT.SalesOrderHeader oh
    ON c.CustomerID = oh.CustomerID
GROUP BY c.Salesperson
ORDER BY SalesRevenue DESC;
```

---

## Filter groups with the HAVING clause

Replace the existing query with the following code:

```sql
SELECT Salesperson, COUNT(CustomerID) AS Customers
FROM SalesLT.Customer
WHERE COUNT(CustomerID) > 100
GROUP BY Salesperson
ORDER BY Salesperson;
```

> This query will produce an error.

Modify the query as follows:

```sql
SELECT Salesperson, COUNT(CustomerID) AS Customers
FROM SalesLT.Customer
GROUP BY Salesperson
HAVING COUNT(CustomerID) > 100
ORDER BY Salesperson;
```

---

## Challenges

### Challenge 1: Retrieve order shipping information

```sql
SELECT SalesOrderID,
       ROUND(Freight, 2) AS FreightCost
FROM SalesLT.SalesOrderHeader;
```

```sql
SELECT SalesOrderID,
       ROUND(Freight, 2) AS FreightCost,
       LOWER(ShipMethod) AS ShippingMethod
FROM SalesLT.SalesOrderHeader;
```

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
SELECT p.Name,SUM(o.OrderQty) AS TotalSales
FROM SalesLT.SalesOrderDetail AS o
JOIN SalesLT.Product AS p
    ON o.ProductID = p.ProductID
GROUP BY p.Name
ORDER BY TotalSales DESC;
```

```sql
SELECT p.Name,SUM(o.OrderQty) AS TotalSales
FROM SalesLT.SalesOrderDetail AS o
JOIN SalesLT.Product AS p
    ON o.ProductID = p.ProductID
WHERE p.ListPrice > 1000
GROUP BY p.Name
ORDER BY TotalSales DESC;
```

```sql
SELECT p.Name,SUM(o.OrderQty) AS TotalSales
FROM SalesLT.SalesOrderDetail AS o
JOIN SalesLT.Product AS p
    ON o.ProductID = p.ProductID
WHERE p.ListPrice > 1000
GROUP BY p.Name
HAVING SUM(o.OrderQty) > 20
ORDER BY TotalSales DESC;
```
