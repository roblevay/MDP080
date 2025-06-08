
Querying Data with Microsoft Transact-SQL  
15 Hr 41 Min Remaining  

# Lab 1  
## Get Started with Transact-SQL

In this exercise, you will use some basic SELECT queries to retrieve data from the AdventureWorks database.

**Note:** This exercise assumes you have created the sample AdventureWorks database.

### Use SELECT queries to retrieve data

The SELECT statement is the primary Transact-SQL statement used to query tables in a database.

Open a query editor for your AdventureWorks database, and create a new query.

In the query editor, enter the following code:

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

Note that the results this time include the Name column and an unnamed numeric column containing the result of subtracting the StandardCost from the ListPrice.

Modify the query as shown below to assign names to the columns in the results, and then re-run the query:

```sql
SELECT Name AS ProductName, ListPrice - StandardCost AS Markup
FROM SalesLT.Product;
```

Note that the results now include columns named ProductName and Markup. The AS keyword has been used to assign an alias for each column in the results.

Replace the existing query with the following code, which also includes an expression that produces a calculated column in the results:

```sql
SELECT ProductNumber, Color, Size, Color + ', ' + Size AS ProductDetails
FROM SalesLT.Product;
```

Run the query, and note that the + operator in the calculated ProductDetails column is used to concatenate the Color and Size column values (with a literal comma between them). The behavior of this operator is determined by the data types of the columns - had they been numeric values, the + operator would have added them. Note also that some results are NULL - we'll explore NULL values later in this lab.

### Work with data types

As you just saw, columns in a table are defined as specific data types, which affects the operations you can perform on them.

Replace the existing query with the following code, and run it:

```sql
SELECT ProductID + ': ' + Name AS ProductName
FROM SalesLT.Product;
```

Note that this query returns an error. The + operator can be used to concatenate text-based values, or add numeric values; but in this case there's one numeric value (ProductID) and one text-based value (Name), so it's unclear how the operator should be applied.

Modify the query as follows, and re-run it:

```sql
SELECT CAST(ProductID AS varchar(5)) + ': ' + Name AS ProductName
FROM SalesLT.Product;
```

Note that the effect of the CAST function is to change the numeric ProductID column into a varchar (variable-length character data) value that can be concatenated with other text-based values.

Modify the query to replace the CAST function with a CONVERT function as shown below, and then re-run it:

```sql
SELECT CONVERT(varchar(5), ProductID) + ': ' + Name AS ProductName
FROM SalesLT.Product;
```

Note that the results of using CONVERT are the same as for CAST. The CAST function is an ANSI standard part of the SQL language that is available in most database systems, while CONVERT is a SQL Server specific function.

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

Note that an error is returned because some Size values are not numeric (for example, some item sizes are indicated as S, M, or L).

Modify the query to use a TRY_CAST function, as shown here:

```sql
SELECT Name, TRY_CAST(Size AS Integer) AS NumericSize
FROM SalesLT.Product;
```

Run the query and note that the numeric Size values are converted successfully to integers, but that non-numeric sizes are returned as NULL.

### Handle NULL values

We've seen some examples of queries that return NULL values. NULL is generally used to denote a value that is unknown.

Modify the existing query as shown here:

```sql
SELECT Name, ISNULL(TRY_CAST(Size AS Integer), 0) AS NumericSize
FROM SalesLT.Product;
```

Replace the query with the following code to handle NULL values for Color and Size:

```sql
SELECT ProductNumber, ISNULL(Color, '') + ', ' + ISNULL(Size, '') AS ProductDetails
FROM SalesLT.Product;
```

Try the following query, which replaces the Color value "Multi" with NULL:

```sql
SELECT Name, NULLIF(Color, 'Multi') AS SingleColor
FROM SalesLT.Product;
```

Use the following query to find the first non-NULL date for product selling status:

```sql
SELECT Name, COALESCE(SellEndDate, SellStartDate) AS StatusLastUpdated
FROM SalesLT.Product;
```

Run the following query, which includes a searched CASE expression:

```sql
SELECT Name,
    CASE
        WHEN SellEndDate IS NULL THEN 'Currently for sale'
        ELSE 'No longer available'
    END AS SalesStatus
FROM SalesLT.Product;
```

Run the following query to see a simple CASE expression:

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

## Challenges

**Tip:** Try to determine the appropriate queries for yourself. If you get stuck, suggested answers are provided at the end of this lab.

### Challenge 1: Retrieve customer data

**Retrieve customer details:**

```sql
SELECT * FROM SalesLT.Customer;
```

**Retrieve customer name data:**

```sql
SELECT Title, FirstName, MiddleName, LastName, Suffix
FROM SalesLT.Customer;
```

**Retrieve customer names and phone numbers:**

```sql
SELECT Salesperson, ISNULL(Title,'') + ' ' + LastName AS CustomerName, Phone
FROM SalesLT.Customer;
```

### Challenge 2: Retrieve customer order data

**Retrieve a list of customer companies:**

```sql
SELECT CAST(CustomerID AS varchar) + ': ' + CompanyName AS CustomerCompany
FROM SalesLT.Customer;
```

**Retrieve a list of sales order revisions:**

```sql
SELECT PurchaseOrderNumber + ' (' + STR(RevisionNumber, 1) + ')' AS OrderRevision,
   CONVERT(nvarchar(30), OrderDate, 102) AS OrderDate
FROM SalesLT.SalesOrderHeader;
```

### Challenge 3: Retrieve customer contact details

**Retrieve customer contact names with middle names if known:**

```sql
SELECT FirstName + ' ' + ISNULL(MiddleName + ' ', '') + LastName AS CustomerName
FROM SalesLT.Customer;
```

**Retrieve primary contact details:**

```sql
UPDATE SalesLT.Customer
SET EmailAddress = NULL
WHERE CustomerID % 7 = 1;
```

```sql
SELECT CustomerID, COALESCE(EmailAddress, Phone) AS PrimaryContact
FROM SalesLT.Customer;
```

**Retrieve shipping status:**

```sql
UPDATE SalesLT.SalesOrderHeader
SET ShipDate = NULL
WHERE SalesOrderID > 71899;
```

```sql
SELECT SalesOrderID, OrderDate,
    CASE
        WHEN ShipDate IS NULL THEN 'Awaiting Shipment'
        ELSE 'Shipped'
    END AS ShippingStatus
FROM SalesLT.SalesOrderHeader;
```
