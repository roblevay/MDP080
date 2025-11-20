

# Lab 1  
## Get Started with Transact-SQL

In this exercise, you will use some basic SELECT queries to retrieve data from the AdventureworksLT database.

**Note:** This exercise assumes you have created the sample AdventureworksLT database.

### Use SELECT queries to retrieve data

The SELECT statement is the primary Transact-SQL statement used to query tables in a database.

Start SQL Server Management Studio and connect to the server North. Open a query editor for your AdventureworksLT database, and create a new query.

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


# Challenges

Now that you've seen some examples of SELECT statements that retrieve data from a table, it's time to try to compose some queries of your own.

**Tip:** Try to determine the appropriate queries for yourself. If you get stuck, suggested answers are provided at the end of this lab.

## Challenge 1: Retrieve customer data

Adventure Works Cycles sells directly to retailers, who then sell products to consumers. Each retailer that is an Adventure Works customer has provided a named contact for all communication from Adventure Works. The sales manager at Adventure Works has asked you to generate some reports containing details of the company’s customers to support a direct sales campaign.

### Retrieve customer details

Familiarize yourself with the `SalesLT.Customer` table by writing a Transact-SQL query that retrieves all columns for all customers.

### Retrieve customer name data

Create a list of all customer contact names that includes the title, first name, middle name (if any), last name, and suffix (if any) of all customers.

### Retrieve customer names and phone numbers

Each customer has an assigned salesperson. You must write a query to create a call sheet that lists:  
- The salesperson  
- A column named `CustomerName` that displays how the customer contact should be greeted (for example, Mr Smith)  
- The customer’s phone number.

## Challenge 2: Retrieve customer order data

As you continue to work with the Adventure Works customer data, you must create queries for reports that have been requested by the sales team.

### Retrieve a list of customer companies

You have been asked to provide a list of all customer companies in the format `Customer ID : Company Name` – for example, `78: Preferred Bikes`.

### Retrieve a list of sales order revisions

The `SalesLT.SalesOrderHeader` table contains records of sales orders. You have been asked to retrieve data for a report that shows:  
- The purchase order number and revision number in the format `PO... (...)` – for example `PO348186287 (2)`  
- The order date converted to ANSI standard 102 format (`yyyy.mm.dd` – for example `2015.01.31`)

## Challenge 3: Retrieve customer contact details

Some records in the database include missing or unknown values that are returned as `NULL`. You must create some queries that handle these `NULL` values appropriately.

### Retrieve customer contact names with middle names if known

You have been asked to write a query that returns a list of customer names. The list must consist of a single column in the format `first last` (for example `Keith Harris`) if the middle name is unknown, or `first middle last` (for example `Jane M. Gates`) if a middle name is known.

### Retrieve primary contact details

Customers may provide Adventure Works with an email address, a phone number, or both.  
If an email address is available, then it should be used as the primary contact method; if not, then the phone number should be used.

You must write a query that returns a list of customer IDs in one column, and a second column named `PrimaryContact` that contains the email address if known, and otherwise the phone number.

**IMPORTANT:** In the sample data provided, there are no customer records without an email address.  
Therefore, to verify that your query works as expected, run the following `UPDATE` statement to remove some existing email addresses before creating your query:

```sql
UPDATE SalesLT.Customer
SET EmailAddress = NULL
WHERE CustomerID % 7 = 1;
```

### Retrieve shipping status

You have been asked to create a query that returns a list of sales order IDs and order dates with a column named `ShippingStatus` that contains:  
- `"Shipped"` for orders with a known ship date  
- `"Awaiting Shipment"` for orders with no ship date

**IMPORTANT:** In the sample data provided, there are no sales order header records without a ship date.  
Therefore, to verify that your query works as expected, run the following `UPDATE` statement to remove some existing ship dates:

```sql
UPDATE SalesLT.SalesOrderHeader
SET ShipDate = NULL
WHERE SalesOrderID > 71899;
```

---

# Challenge Solutions

## Challenge 1

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

## Challenge 2

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

## Challenge 3

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
