## Lab 1

**Get Started with Transact-SQL**
In this exercise, you will use some basic SELECT queries to retrieve data from the AdventureWorks database.

> **Note**: This exercise assumes you have created the sample AdventureWorks database.

---

### Use SELECT queries to retrieve data

The SELECT statement is the primary Transact-SQL statement used to query tables in a database.

Open a query editor for your AdventureWorks database, and create a new query.

```sql
SELECT * FROM SalesLT.Product;
```

Run the query, and and after a few seconds, review the results, which includes all columns for all products.

In the query editor, modify the query as follows:

```sql
SELECT Name, StandardCost, ListPrice
FROM SalesLT.Product;
```

Re-run the query, and and after a few seconds, review the results, which this time include only the Name, StandardCost, and ListPrice columns for all products.

Modify the query as shown below to include an expression that results in a calculated column, and then re-run the query:

```sql
SELECT Name, ListPrice - StandardCost
FROM SalesLT.Product;
```

Modify the query as shown below to assign names to the columns in the results, and then re-run the query.

```sql
SELECT Name AS ProductName, ListPrice - StandardCost AS Markup
FROM SalesLT.Product;
```

Replace the existing query with the following code, which also includes an expression that produces a calculated column in the results:

```sql
SELECT ProductNumber, Color, Size, Color + ', ' + Size AS ProductDetails
FROM SalesLT.Product;
```

---

### Work with data types

Replace the existing query with the following code, and run it:

```sql
SELECT ProductID + ': ' + Name AS ProductName
FROM SalesLT.Product; 
```

Modify the query as follows, and re-run it:

```sql
SELECT CAST(ProductID AS varchar(5)) + ': ' + Name AS ProductName
FROM SalesLT.Product; 
```

Modify the query to replace the CAST function with a CONVERT function as shown below, and then re-run it:

```sql
SELECT CONVERT(varchar(5), ProductID) + ': ' + Name AS ProductName
FROM SalesLT.Product; 
```

Another key difference between the two functions is that CONVERT includes an additional parameter that can be useful for formatting date and time values when converting them to text-based data. For example:

```sql
SELECT SellStartDate,
   CONVERT(nvarchar(30), SellStartDate) AS ConvertedDate,
   CONVERT(nvarchar(30), SellStartDate, 126) AS ISO8601FormatDate
FROM SalesLT.Product; 
```

Replace the existing query with the following code, and run it:

```sql
SELECT Name, CAST(Size AS Integer) AS NumericSize
FROM SalesLT.Product; 
```

Modify the query to use a TRY\_CAST function, as shown here:

```sql
SELECT Name, TRY_CAST(Size AS Integer) AS NumericSize
FROM SalesLT.Product; 
```

---

### Handle NULL values

Modify the existing query as shown here:

```sql
SELECT Name, ISNULL(TRY_CAST(Size AS Integer),0) AS NumericSize
FROM SalesLT.Product;
```

Replace the query with the following code:

```sql
SELECT ProductNumber, ISNULL(Color, '') + ', ' + ISNULL(Size, '') AS ProductDetails
FROM SalesLT.Product;
```

Try the following query, which replaces the Color value "Multi" to NULL.

```sql
SELECT Name, NULLIF(Color, 'Multi') AS SingleColor
FROM SalesLT.Product;
```

Use the following query to find the first non-NULL date for product selling status.

```sql
SELECT Name, COALESCE(SellEndDate, SellStartDate) AS StatusLastUpdated
FROM SalesLT.Product;
```

Run the following query, which includes searched CASE:

```sql
SELECT Name,
    CASE
        WHEN SellEndDate IS NULL THEN 'Currently for sale'
        ELSE 'No longer available'
    END AS SalesStatus
FROM SalesLT.Product;
```

Run the following query to see an example of a simple CASE expression:

```sql
SELECT Name,
    CASE Size
        WHEN 'S' THEN 'Small'
        WHEN 'M' THEN 'Medium'
        WHEN 'L' THEN 'Large'
        WHEN 'XL' THEN 'Extra-Large'
        ELSE ISNULL(Size, 'n/a')
    END AS ProductSize
FROM SalesLT.Product;
```

---

## Challenges

> **Tip**: Try to determine the appropriate queries for yourself. If you get stuck, suggested answers are provided at the end of this lab.

### Challenge 1: Retrieve customer data

* **Retrieve customer details**
* **Retrieve customer name data**
* **Retrieve customer names and phone numbers**

### Challenge 2: Retrieve customer order data

* **Retrieve a list of customer companies**
* **Retrieve a list of sales order revisions**

### Challenge 3: Retrieve customer contact details

* **Retrieve customer contact names with middle names if known**
* **Retrieve primary contact details**
* **Retrieve shipping status**

> **Note**: Use the following before queries to test NULL handling:

```sql
UPDATE SalesLT.Customer
SET EmailAddress = NULL
WHERE CustomerID % 7 = 1;
```

```sql
UPDATE SalesLT.SalesOrderHeader
SET ShipDate = NULL
WHERE SalesOrderID > 71899;
```

---

## Challenge Solutions

### Challenge 1

```sql
SELECT * FROM SalesLT.Customer;
```

```sql
SELECT Title, FirstName, MiddleName, LastName, Suffix
FROM SalesLT.Customer;
```

```sql
SELECT Salesperson, ISNULL(Title,'') + ' ' + LastName AS CustomerName, Phone
FROM SalesLT.Customer;
```

### Challenge 2

```sql
SELECT CAST(CustomerID AS varchar) + ': ' + CompanyName AS CustomerCompany
FROM SalesLT.Customer;
```

```sql
SELECT PurchaseOrderNumber + ' (' + STR(RevisionNumber, 1) + ')' AS OrderRevision,
   CONVERT(nvarchar(30), OrderDate, 102) AS OrderDate
FROM SalesLT.SalesOrderHeader;
```

### Challenge 3

```sql
SELECT FirstName + ' ' + ISNULL(MiddleName + ' ', '') + LastName AS CustomerName
FROM SalesLT.Customer;
```

```sql
SELECT CustomerID, COALESCE(EmailAddress, Phone) AS PrimaryContact
FROM SalesLT.Customer;
```

```sql
SELECT SalesOrderID, OrderDate,
    CASE
        WHEN ShipDate IS NULL THEN 'Awaiting Shipment'
        ELSE 'Shipped'
    END AS ShippingStatus
FROM SalesLT.SalesOrderHeader;
```

---

*End of Lab 1 Instructions, Challenges, and Solutions*
