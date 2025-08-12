# **M Query Master Cheat Sheet**

M is the functional, case-sensitive language behind Power Query. It's used for data extraction, transformation, and mashup.

#### **1. The Basic Structure: `let ... in`**

Every M query is a `let` expression. It's a series of steps (variables) where each step can build upon the previous one.

```m
let
    // Step 1: Give it a name, define what it does.
    Source = Csv.Document(File.Contents("C:\Data\Sales.csv"), [Delimiter=","]),

    // Step 2: This step uses the result of the 'Source' step.
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),

    // Step 3: This step uses the result of the 'Promoted Headers' step.
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Date", type date}, {"Amount", Currency.Type}})

// The 'in' statement defines the final output of the query.
in
    #"Changed Type"
```

**Pro Tip:** Use the UI to generate the basic code, then go into the Advanced Editor to refine it. Name your steps clearly for readability (e.g., `FilteredInactiveProducts` instead of `#"Filtered Rows1"`).

---

#### **2. Common Data Sources**

| Function | Description | Example |
| :--- | :--- | :--- |
| `Excel.Workbook()` | Get data from an Excel file. | `Excel.Workbook(File.Contents("C:\data.xlsx"))` |
| `Csv.Document()` | Get data from a CSV file. | `Csv.Document(File.Contents("C:\data.csv"))` |
| `Json.Document()` | Get data from a JSON file/source. | `Json.Document(Web.Contents("api.com/data"))` |
| `Sql.Database()` | Get data from a SQL Server DB. | `Sql.Database("serverName", "databaseName")` |
| `Folder.Files()` | Get all files from a folder. | `Folder.Files("C:\Monthly Reports\")` |

---

#### **3. Table Transformations (The Core of M)**

These functions all start with `Table.` and operate on the entire table.

| Function | Description | Example (within a `let` block) |
| :--- | :--- | :--- |
| `Table.SelectColumns` | Keep only specified columns. | `Table.SelectColumns(Source, {"Date", "Product", "Sales"})` |
| `Table.RemoveColumns` | Remove specified columns. | `Table.RemoveColumns(Source, {"InternalID", "Notes"})` |
| `Table.SelectRows` | Filter rows based on a condition. | `Table.SelectRows(Source, each [Country] = "USA")` |
| `Table.Sort` | Sort the table by one or more columns. | `Table.Sort(Source, {{"Country", Order.Ascending}, {"Sales", Order.Descending}})` |
| `Table.AddColumn` | Add a new column with a calculated value. | `Table.AddColumn(Source, "Margin", each [Sales] - [Cost])` |
| `Table.DuplicateColumn` | Duplicate an existing column. | `Table.DuplicateColumn(Source, "Product", "Product Copy")` |
| `Table.Group` | Group rows and aggregate values (like SQL's GROUP BY). | `Table.Group(Source, {"Country"}, {{"TotalSales", each List.Sum([Sales]), type number}})` |
| `Table.Distinct` | Keep only unique rows. | `Table.Distinct(Source, {"CustomerID"})` |
| `Table.Buffer` | Load a table into memory. Prevents re-running source queries. | `Table.Buffer(Source)` |

---

#### **4. Column Transformations**

| Function | Description | Example |
| :--- | :--- | :--- |
| `Table.RenameColumns` | Rename one or more columns. | `Table.RenameColumns(Source, {{"Cust_ID", "CustomerID"}})` |
| `Table.ReorderColumns` | Change the order of columns. | `Table.ReorderColumns(Source, {"CustomerID", "Name", "Sales"})` |
| `Table.TransformColumnTypes` | Change the data type of columns. **Crucial for performance!** | `Table.TransformColumnTypes(Source, {{"Date", type date}, {"Sales", Currency.Type}})` |
| `Table.SplitColumn` | Split a column by a delimiter. | `Table.SplitColumn(Source, "Name", Splitter.SplitTextByDelimiter(" "), {"FirstName", "LastName"})` |
| `Table.ReplaceValue` | Find and replace values in a column. | `Table.ReplaceValue(Source, "USA", "United States", Replacer.ReplaceText, {"Country"})` |

---

#### **5. Combining Queries**

| Function | Description | Equivalent In UI |
| :--- | :--- | :--- |
| `Table.NestedJoin` | Combine two tables based on a key column (like SQL JOIN). | **Merge Queries** |
| `Table.Combine` | Stack two or more tables with the same structure on top of each other (like SQL UNION ALL). | **Append Queries** |

**Join Example:**
```m
Table.NestedJoin(
    SalesTable,         // Left table
    {"ProductID"},      // Key in left table
    ProductsTable,      // Right table
    {"ProductID"},      // Key in right table
    "NewColumnName",    // Name for the new column holding the nested table
    JoinKind.LeftOuter  // Join Type (Inner, LeftOuter, RightOuter, FullOuter)
)
```

---

#### **6. Text Functions**

Used inside `Table.AddColumn` or `Table.TransformColumns`.

| Function | Description | Example (in a new column) |
| :--- | :--- | :--- |
| `Text.Combine` | Join a list of text values with a separator. | `Text.Combine({[FirstName], [LastName]}, " ")` |
| `Text.Start` / `Text.End` | Get the first/last N characters. | `Text.Start([ProductCode], 3)` |
| `Text.Trim` / `Text.Clean` | Remove whitespace / non-printable characters. | `Text.Trim([ProductName])` |
| `Text.Upper` / `Text.Lower` | Convert to upper/lower case. | `Text.Upper([CountryCode])` |
| `Text.Contains` | Check if text contains a substring (returns true/false). | `Text.Contains([Comment], "urgent")` |

---

#### **7. Conditional Logic: `if ... then ... else`**

This is essential for creating custom columns or logic.

**Example: Add a "Sales Category" column**
```m
Table.AddColumn(Source, "Sales Category", each
    if [Sales] > 1000 then "High Value"
    else if [Sales] > 500 then "Medium Value"
    else "Low Value"
)
```

---

#### **8. Error Handling: `try ... otherwise`**

Prevents your entire query from failing if one row has an error (e.g., during a data type conversion).

**Example: Safely convert a column to a number, returning null on error.**
```m
Table.TransformColumns(Source, {{"Sales", each
    try Number.FromText(_) otherwise null, type number
}})
```
*The `_` represents the current value in the column being processed.*

---

#### **9. Advanced Concepts**

*   **Custom Functions:** Create your own reusable logic.
    ```m
    // (parameter1 as type, parameter2 as type) => calculation
    (Name as text) as text => "Hello, " & Name
    ```

*   **Pivoting / Unpivoting:**
    *   `Table.UnpivotOtherColumns`: The most common way to transform a "wide" table to a "long" table.
    *   `Table.Pivot`: Transforms a "long" table to a "wide" table.

*   **Query Folding:** This is when Power Query translates your M steps into the native language of the source (e.g., SQL). **This is the single most important concept for performance with databases.**
    *   **To preserve folding:** Use transformations that can be translated to SQL (filter, sort, group, join).
    *   **To break folding:** Using M functions that don't have a SQL equivalent (like `Table.AddIndexColumn`) will force Power BI to load all the data *before* continuing the transformations.

---
## **Advanced M Query Examples**

#### **Scenario 1: Get the Most Recent Record Within Each Category**

This is a very common requirement, such as finding the latest status update for each project or the most recent purchase for each customer.

**The Scenario:**
You have a table of product sales with multiple entries for each product on different dates. You need a table that shows only the single most recent sale (the entire row) for each unique product.

**The Challenge:**
The standard `Group By` feature only lets you aggregate data (e.g., get the `MAX` date), but it doesn't easily return the *other columns* from that specific row (like the sales amount or sales-person on that max date).

**The M Query Solution:**
This solution groups the data by `ProductID` and then uses a list function to find the record with the maximum date within each group.

```m
let
    // Sample Data Source
    Source = Table.FromRecords({
        [ProductID=1, Date=#date(2023,1,10), Sales=100, Salesperson="Bob"],
        [ProductID=2, Date=#date(2023,1,12), Sales=150, Salesperson="Sue"],
        [ProductID=1, Date=#date(2023,1,25), Sales=120, Salesperson="Bob"], // This is the latest for Product 1
        [ProductID=3, Date=#date(2023,1,5),  Sales=200, Salesperson="Tom"],
        [ProductID=2, Date=#date(2023,1,18), Sales=160, Salesperson="Sue"], // This is the latest for Product 2
        [ProductID=3, Date=#date(2023,1,20), Sales=210, Salesperson="Tom"]  // This is the latest for Product 3
    }, type table [ProductID=Int64.Type, Date=Date.Type, Sales=Int64.Type, Salesperson=Text.Type]),

    // Group the table by ProductID. This creates a nested table for each product.
    #"Grouped Rows" = Table.Group(Source, {"ProductID"}, {
        // Create a new column "LatestRecord" for each group.
        // 'each' refers to the nested table of data for one ProductID.
        {"LatestRecord", each
            // Find the record(s) within the sub-table that have the maximum date.
            let
                MaxDate = List.Max([Date]) // Find the latest date in the current product's sub-table
            in
                Table.SelectRows(_, (row) => row[Date] = MaxDate) // Select the full row where the date matches the max date
        , type table}
    }),

    // The "LatestRecord" column now contains a table with a single row. Expand it.
    #"Expanded LatestRecord" = Table.ExpandTableColumn(#"Grouped Rows", "LatestRecord",
        {"Date", "Sales", "Salesperson"}, // List the columns you want to bring out
        {"Date", "Sales", "Salesperson"}) // New names for the expanded columns
in
    #"Expanded LatestRecord"
```
**Explanation:**
1.  `Table.Group` bundles all rows for each `ProductID` into a nested table.
2.  Inside the grouping, we operate on that small, nested table (`_`).
3.  `List.Max([Date])` finds the latest date *within that specific group*.
4.  `Table.SelectRows` then filters the nested table to find the entire row corresponding to that max date.
5.  Finally, `Table.ExpandTableColumn` flattens the result to get the final table.

---

#### **Scenario 2: Conditional Join (Non-Equi Join)**

You need to join two tables where the condition is not a simple equality (`A.key = B.key`) but involves a range, like a date falling between a start and end date.

**The Scenario:**
You have a `Sales` table and a `Promotions` table. You want to match each sale with the promotion that was active on the sale date.

**The Challenge:**
The standard `Merge Queries` UI only allows joining on columns being equal. It cannot handle "between" logic (`SaleDate >= PromoStart AND SaleDate <= PromoEnd`).

**The M Query Solution:**
We add a custom column to the `Sales` table. For each sales row, it filters the *entire* `Promotions` table to find the matching promotion and returns its name.

```m
let
    // Sales Table
    Sales = Table.FromRecords({
        [SaleID=1, SaleDate=#date(2023,1,5), Amount=50],
        [SaleID=2, SaleDate=#date(2023,1,15), Amount=120],
        [SaleID=3, SaleDate=#date(2023,2,10), Amount=200]
    }, type table [SaleID=Int64.Type, SaleDate=Date.Type, Amount=Int64.Type]),

    // Promotions Table
    Promotions = Table.FromRecords({
        [PromoName="New Year Sale", StartDate=#date(2023,1,1), EndDate=#date(2023,1,10)],
        [PromoName="Winter Special", StartDate=#date(2023,1,11), EndDate=#date(2023,1,31)],
        [PromoName="Valentines Deal", StartDate=#date(2023,2,1), EndDate=#date(2023,2,14)]
    }, type table [PromoName=Text.Type, StartDate=Date.Type, EndDate=Date.Type]),

    // Add a custom column to the Sales table
    #"Added Promotion" = Table.AddColumn(Sales, "Promotion Name", (CurrentSalesRow) =>
        // For each row in the Sales table, filter the Promotions table
        let
            MatchingPromo = Table.SelectRows(Promotions, each
                CurrentSalesRow[SaleDate] >= [StartDate] and
                CurrentSalesRow[SaleDate] <= [EndDate]
            )
        in
            // Get the PromoName from the first matching row. Use '?' for safety if no match is found.
            Table.First(MatchingPromo)?[PromoName],
        type text
    )
in
    #"Added Promotion"
```
**Explanation:**
1.  `Table.AddColumn` iterates through each row of the `Sales` table. We call the context of the current row `CurrentSalesRow`.
2.  Inside the custom column logic, `Table.SelectRows` filters the *entire* `Promotions` table based on the `SaleDate` from the `CurrentSalesRow`.
3.  This returns a (potentially empty) table of matching promotions.
4.  `Table.First(…)[PromoName]` extracts the name from the first record of that filtered table. The `?` (optional item operator) prevents an error if no match is found, returning `null` instead.

---

#### **Scenario 3: Dynamic Unpivoting**

You have data where new columns are added over time (e.g., a new column for each month). You want to unpivot these columns without having to manually edit the query every time the source changes.

**The Scenario:**
Your data has an `ID` column and then a separate column for each month: `Jan-23`, `Feb-23`, `Mar-23`, etc. Next month, `Apr-23` will be added. You want to unpivot these month columns dynamically.

**The Challenge:**
The standard `Unpivot Columns` UI hardcodes the names of the columns you are unpivoting. When a new month column is added to the source file, it will be ignored by your query.

**The M Query Solution:**
We get a list of all column names, remove the ones we want to keep fixed (the "ID" columns), and then use that dynamic list in the unpivot operation.

```m
let
    Source = Table.FromRecords({
        [StoreID="A", Region="North", #"Jan-23"=100, #"Feb-23"=110, #"Mar-23"=120],
        [StoreID="B", Region="South", #"Jan-23"=200, #"Feb-23"=210, #"Mar-23"=220]
    }),

    // Get the list of all column names from the source table
    AllColumnNames = Table.ColumnNames(Source),

    // Define the columns you DO NOT want to unpivot
    IDColumns = {"StoreID", "Region"},

    // Create a list of columns TO UNPIVOT by removing the ID columns from the full list
    ColumnsToUnpivot = List.RemoveItems(AllColumnNames, IDColumns),

    // Perform the unpivot using the dynamic list of column names
    #"Unpivoted Other Columns" = Table.Unpivot(Source, ColumnsToUnpivot, "Month", "Sales")

in
    #"Unpivoted Other Columns"
```
**Explanation:**
1.  `Table.ColumnNames(Source)` creates a list of all column headers (e.g., `{"StoreID", "Region", "Jan-23", ...}`).
2.  `List.RemoveItems()` subtracts the list of fixed columns from the list of all columns, resulting in a dynamic list of only the month columns.
3.  `Table.Unpivot` (the function behind the "Unpivot Other Columns" button) is used here. We explicitly tell it which columns to unpivot. Since that list is generated dynamically, the query will work even if new month columns are added later.
