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
Save this guide. It will help you solve over 90% of the data transformation challenges you'll face in Power BI. Good luck
