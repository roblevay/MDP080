# Querying Data with Microsoft Transact-SQL

## Lab 1: Get Started with Transact-SQL

### Introduction

In this exercise, you will use basic `SELECT` queries to retrieve data from the **AdventureWorks** database.

> **Note**: This exercise assumes the sample AdventureWorks database has already been created.

---

### Using SELECT Queries to Retrieve Data

The `SELECT` statement is the primary Transact-SQL command for retrieving data.

**Step 1: Open the Query Editor**
Open a query editor for your AdventureWorks database and create a new query.

**Step 2: Retrieve All Columns:**

```sql
SELECT * FROM SalesLT.Product;
```

Run the query and review the results, which include all columns for all products.

**Step 3: Select Specific Columns:**

```sql
SELECT Name, StandardCost, ListPrice
FROM SalesLT.Product;
```

Re-run the query to display only the `Name`, `StandardCost`, and `ListPrice` columns.

**Step 4: Add Calculated Column:**

```sql
SELECT Name, ListPrice - StandardCost
FROM SalesLT.Product;
```

Returns `Name` and a numeric column showing the markup. The column is unnamed.

**Step 5: Alias Columns:**

```sql
SELECT Name AS ProductName, ListPrice - StandardCost AS Markup
FROM SalesLT.Product;
```

Uses the `AS` keyword to assign column aliases.

**Step 6: Concatenate Strings:**

```sql
SELECT ProductNumber, Color, Size, Color + ', ' + Size AS ProductDetails
FROM SalesLT.Product;
```

Concatenates `Color` and `Size`. Note: Some results may contain NULLs.

---

### Working with Data Types

**Step 7: Attempt Invalid Concatenation:**

```sql
SELECT ProductID + ': ' + Name AS ProductName
FROM SalesLT.Product;
```

This query returns an error due to incompatible types.

**Step 8: Convert Numeric to Text with CAST:**

```sql
SELECT CAST(ProductID AS varchar(5)) + ': ' + Name AS ProductName
FROM SalesLT.Product; 
```

**Step 9: Use SQL Server-Specific CONVERT:**

```sql
SELECT CONVERT(varchar(5), ProductID) + ': ' + Name AS ProductName
FROM SalesLT.Product; 
```

**Step 10: Format Dates with CONVERT:**

```sql
SELECT SellStartDate,
   CONVERT(nvarchar(30), SellStartDate) AS ConvertedDate,
   CONVERT(nvarchar(30), SellStartDate, 126) AS ISO8601FormatDate
FROM SalesLT.Product;
```

**Step 11: Try to Convert Non-Numeric Text:**

```sql
SELECT Name, CAST(Size AS Integer) AS NumericSize
FROM SalesLT.Product;
```

Returns error if some `Size` values are not numeric.

**Step 12: Handle Errors with TRY\_CAST:**

```sql
SELECT Name, TRY_CAST(Size AS Integer) AS NumericSize
FROM SalesLT.Product;
```

---

### Handling NULL Values

**Step 13: Replace NULL with Default Value:**

```sql
SELECT Name, ISNULL(TRY_CAST(Size AS Integer), 0) AS NumericSize
FROM SalesLT.Product;
```

**Step 14: Replace NULLs in Source Columns:**

```sql
SELECT ProductNumber, ISNULL(Color, '') + ', ' + ISNULL(Size, '') AS ProductDetails
FROM SalesLT.Product;
```

**Step 15: Replace Specific Value with NULL:**

```sql
SELECT Name, NULLIF(Color, 'Multi') AS SingleColor
FROM SalesLT.Product;
```

**Step 16: Find First Non-NULL Column:**

```sql
SELECT Name, COALESCE(SellEndDate, SellStartDate) AS StatusLastUpdated
FROM SalesLT.Product;
```

**Step 17: Use CASE to Check Sales Status:**

```sql
SELECT Name,
    CASE
        WHEN SellEndDate IS NULL THEN 'Currently for sale'
        ELSE 'No longer available'
    END AS SalesStatus
FROM SalesLT.Product;
```

**Step 18: Use Simple CASE for Size Descriptions:**

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

Now that you've seen some examples of `SELECT` statements that retrieve data from a table, it's time to try to compose some queries of your own.

> **Tip:** Try to determine the appropriate queries for yourself. If you get stuck, suggested answers are provided at the end of this lab.

### Challenge 1: Retrieve Customer Data

Adventure Works Cycles sells directly to retailers, who then sell products to consumers. Each retailer that is an Adventure Works customer has provided a named contact for all communication from Adventure Works. The sales manager at Adventure Works has asked you to generate some reports containing details of the company’s customers to support a direct sales campaign.

* **Retrieve customer details:** Familiarize yourself with the `SalesLT.Customer` table by writing a Transact-SQL query that retrieves all columns for all customers.
* **Retrieve customer name data:** Create a list of all customer contact names that includes the title, first name, middle name (if any), last name, and suffix (if any).
* **Retrieve customer names and phone numbers:** Write a query to create a call sheet that lists:

  * The salesperson
  * A column named `CustomerName` that displays how the customer contact should be greeted (e.g., Mr Smith)
  * The customer’s phone number

### Challenge 2: Retrieve Customer Order Data

As you continue to work with the Adventure Works customer data, you must create queries for reports requested by the sales team.

* **Retrieve a list of customer companies:** Format as `CustomerID : CompanyName` (e.g., `78: Preferred Bikes`).
* **Retrieve a list of sales order revisions:**

  * Show the purchase order number and revision number in the format `POxxxxx (n)`
  * Convert the order date to ANSI standard 102 format (`yyyy.mm.dd`)

### Challenge 3: Retrieve Customer Contact Details

Some records in the database include missing or unknown values that are returned as NULL. You must create some queries that handle these NULL values appropriately.

* **Retrieve customer contact names with middle names if known:** Return names as `First Last` if the middle name is NULL, otherwise `First Middle Last`.
* **Retrieve primary contact details:** Return a list of customer IDs and a `PrimaryContact` column that contains the email address if known, otherwise the phone number.

> **Important:** To verify the query works, run:

```sql
UPDATE SalesLT.Customer
SET EmailAddress = NULL
WHERE CustomerID % 7 = 1;
```

* **Retrieve shipping status:** Return a list of sales order IDs and order dates, with a column named `ShippingStatus` containing:

  * `Shipped` if a ship date is known
  * `Awaiting Shipment` if the ship date is NULL

> **Important:** To simulate missing ship dates, run:

```sql
UPDATE SalesLT.SalesOrderHeader
SET ShipDate = NULL
WHERE SalesOrderID > 71899;
```

---

## Challenge Solutions

This section contains suggested solutions for the challenge queries.

### Challenge 1

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

### Challenge 2

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

### Challenge 3

**Retrieve customer contact names with middle names if known:**

```sql
SELECT FirstName + ' ' + ISNULL(MiddleName + ' ', '') + LastName AS CustomerName
FROM SalesLT.Customer;
```

**Retrieve primary contact details:**

```sql
SELECT CustomerID, COALESCE(EmailAddress, Phone) AS PrimaryContact
FROM SalesLT.Customer;
```

**Retrieve shipping status:**

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
