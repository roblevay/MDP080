## Lab 3a

**Query Multiple Tables with Joins**
In this exercise, you'll use the Transact-SQL SELECT statement to query multiple tables in the AdventureWorks database.

> **Note**: This exercise assumes you have created the sample AdventureWorks database.

---

### Use inner joins

Open a query editor for your AdventureWorks database, and create a new query.

```sql
SELECT SalesLT.Product.Name AS ProductName, SalesLT.ProductCategory.Name AS Category
FROM SalesLT.Product
INNER JOIN SalesLT.ProductCategory
ON SalesLT.Product.ProductCategoryID = SalesLT.ProductCategory.ProductCategoryID;
```

Remove the `INNER` keyword and re-run it:

```sql
SELECT SalesLT.Product.Name AS ProductName, SalesLT.ProductCategory.Name AS Category
FROM SalesLT.Product
JOIN SalesLT.ProductCategory
    ON SalesLT.Product.ProductCategoryID = SalesLT.ProductCategory.ProductCategoryID;
```

Add table aliases:

```sql
SELECT p.Name AS ProductName, c.Name AS Category
FROM SalesLT.Product AS p
JOIN SalesLT.ProductCategory AS c
    ON p.ProductCategoryID = c.ProductCategoryID;
```

Query three tables:

```sql
SELECT oh.OrderDate, oh.PurchaseOrderNumber, p.Name AS ProductName, od.OrderQty, od.UnitPrice
FROM SalesLT.SalesOrderHeader AS oh
JOIN SalesLT.SalesOrderDetail AS od
    ON od.SalesOrderID = oh.SalesOrderID
JOIN SalesLT.Product AS p
    ON od.ProductID = p.ProductID
ORDER BY oh.OrderDate, oh.SalesOrderID, od.SalesOrderDetailID;
```

---

### Use outer joins

```sql
SELECT c.FirstName, c.LastName, oh.PurchaseOrderNumber
FROM SalesLT.Customer AS c
LEFT OUTER JOIN SalesLT.SalesOrderHeader AS oh
    ON c.CustomerID = oh.CustomerID
ORDER BY c.CustomerID;
```

Remove `OUTER` keyword:

```sql
SELECT c.FirstName, c.LastName, oh.PurchaseOrderNumber
FROM SalesLT.Customer AS c
LEFT JOIN SalesLT.SalesOrderHeader AS oh
    ON c.CustomerID = oh.CustomerID
ORDER BY c.CustomerID;
```

Show only customers with no orders:

```sql
SELECT c.FirstName, c.LastName, oh.PurchaseOrderNumber
FROM SalesLT.Customer AS c
LEFT JOIN SalesLT.SalesOrderHeader AS oh
    ON c.CustomerID = oh.CustomerID
WHERE oh.SalesOrderNumber IS NULL 
ORDER BY c.CustomerID;
```

Join three tables with outer joins:

```sql
SELECT p.Name As ProductName, oh.PurchaseOrderNumber
FROM SalesLT.Product AS p
LEFT JOIN SalesLT.SalesOrderDetail AS od
    ON p.ProductID = od.ProductID
LEFT JOIN SalesLT.SalesOrderHeader AS oh
    ON od.SalesOrderID = oh.SalesOrderID
ORDER BY p.ProductID;
```

Mix with inner join:

```sql
SELECT p.Name As ProductName, c.Name AS Category, oh.PurchaseOrderNumber
FROM SalesLT.Product AS p
LEFT OUTER JOIN SalesLT.SalesOrderDetail AS od
    ON p.ProductID = od.ProductID
LEFT OUTER JOIN SalesLT.SalesOrderHeader AS oh
    ON od.SalesOrderID = oh.SalesOrderID
INNER JOIN SalesLT.ProductCategory AS c
    ON p.ProductCategoryID = c.ProductCategoryID
ORDER BY p.ProductID;
```

---

### Use a cross join

```sql
SELECT p.Name, c.FirstName, c.LastName, c.EmailAddress
FROM SalesLT.Product AS p
CROSS JOIN SalesLT.Customer AS c;
```

---

### Use a self join

```sql
SELECT pcat.Name AS ParentCategory, cat.Name AS SubCategory
FROM SalesLT.ProductCategory AS cat
JOIN SalesLT.ProductCategory pcat
    ON cat.ParentProductCategoryID = pcat.ProductCategoryID
ORDER BY ParentCategory, SubCategory;
```

---

## Challenges

> **Tip**: Try to determine the appropriate queries for yourself. If you get stuck, suggested answers are provided at the end of this lab.

### Challenge 1: Generate invoice reports

* Retrieve company name, purchase order number, and total due from customer and order tables.
* Extend query to include full main office address by joining CustomerAddress and Address.

### Challenge 2: Retrieve customer data

* Retrieve all customer companies and contacts, with order details. Include customers with no orders.
* Retrieve list of customers with no address.

### Challenge 3: Create a product catalog

* Retrieve parent category, subcategory, and product name.

---

## Challenge Solutions

### Challenge 1

```sql
SELECT c.CompanyName,
       oh.PurchaseOrderNumber,
       oh.SubTotal + oh.TaxAmt + oh.Freight As TotalDue
FROM SalesLT.Customer AS c
JOIN SalesLT.SalesOrderHeader AS oh
    ON oh.CustomerID = c.CustomerID;
```

```sql
SELECT c.CompanyName,
       a.AddressLine1,
       ISNULL(a.AddressLine2, '') AS AddressLine2,
       a.City,
       a.StateProvince,
       a.PostalCode,
       a.CountryRegion,
       oh.PurchaseOrderNumber,
       oh.SubTotal + oh.TaxAmt + oh.Freight As TotalDue
FROM SalesLT.Customer AS c
JOIN SalesLT.SalesOrderHeader AS oh
    ON oh.CustomerID = c.CustomerID
JOIN SalesLT.CustomerAddress AS ca
    ON c.CustomerID = ca.CustomerID
JOIN SalesLT.Address AS a
    ON ca.AddressID = a.AddressID
WHERE ca.AddressType = 'Main Office';
```

### Challenge 2

```sql
SELECT c.CompanyName, c.FirstName, c.LastName,
       oh.PurchaseOrderNumber,
       oh.SubTotal + oh.TaxAmt + oh.Freight As TotalDue
FROM SalesLT.Customer AS c
LEFT JOIN SalesLT.SalesOrderHeader AS oh
    ON c.CustomerID = oh.CustomerID
ORDER BY oh.PurchaseOrderNumber DESC;
```

```sql
SELECT c.CompanyName, c.FirstName, c.LastName, c.Phone
FROM SalesLT.Customer AS c
LEFT JOIN SalesLT.CustomerAddress AS ca
    ON c.CustomerID = ca.CustomerID
WHERE ca.AddressID IS NULL;
```

### Challenge 3

```sql
SELECT pcat.Name AS ParentCategory, cat.Name AS SubCategory, prd.Name AS ProductName
FROM SalesLT.ProductCategory pcat
JOIN SalesLT.ProductCategory AS cat
    ON pcat.ProductCategoryID = cat.ParentProductCategoryID
JOIN SalesLT.Product AS prd
    ON prd.ProductCategoryID = cat.ProductCategoryID
ORDER BY ParentCategory, SubCategory, ProductName;
```



