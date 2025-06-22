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

## **DAX Master Cheat Sheet**

DAX is the formula language for creating custom calculations in Power BI, Analysis Services, and Power Pivot. It is used to define **Calculated Columns** and **Measures**.

#### **1. The Core Concepts: Calculated Columns vs. Measures**

This is the most fundamental concept in DAX. Understanding the difference is critical.

| Feature | Calculated Column | Measure |
| :--- | :--- | :--- |
| **Calculation Time**| At **data refresh**. The result is stored in the model. | At **query time**. Calculated on-the-fly when you use it in a visual. |
| **Storage** | **Consumes RAM and disk space.** Increases the file size. | **Does not consume RAM/space.** Only the formula is stored. |
| **Row Context** | Evaluated **row-by-row**. It can see the values of other columns in the *same row*. | Evaluated in the **filter context** of a visual or slicer. It does not have an inherent row context. |
| **When to Use** | When you need to **filter or slice** by the result, or see the static value for each row in a table. | For **aggregations** that respond to user interaction (e.g., Total Sales, % of Total, Year-over-Year growth). |
| **Example** | `Price Category = IF(Products[Price] > 100, "High", "Low")` | `Total Sales = SUM(Sales[Amount])` |

**Rule of Thumb:** Use a **Measure** whenever possible. Only use a **Calculated Column** when you absolutely must.

---

#### **2. The Superpower of DAX: `CALCULATE()`**

If you learn only one function, make it `CALCULATE`. It is the most powerful and important function in DAX. It **modifies the filter context** to perform calculations.

**Syntax:** `CALCULATE( <expression>, <filter1>, <filter2>, ... )`

*   **`<expression>`:** The measure or calculation you want to evaluate (e.g., `SUM(Sales[Amount])`).
*   **`<filter>`:** A condition that modifies the context. It can be a simple filter or a powerful table function.

**Example:** Calculate sales for only the "USA". This overrides any country selection in a slicer.
```dax
Sales USA = CALCULATE( [Total Sales], 'Geography'[Country] = "USA" )
```

---

#### **3. Key DAX Functions by Category**

##### **A) Aggregation & Iterator Functions (`...` vs `...X`)**

*   **Standard Aggregators:** Operate over a single column.
    *   `SUM()`, `AVERAGE()`, `MIN()`, `MAX()`, `COUNT()`
    *   `DISTINCTCOUNT()`: Counts the number of unique values in a column.
*   **Iterator Functions (`X` functions):** Operate **row-by-row** over a table, then perform an aggregation.
    *   `SUMX(<table>, <expression>)`: Iterates the table, evaluates the expression for each row, then sums the results.
    *   `AVERAGEX()`, `MINX()`, `MAXX()`, `COUNTX()`

**Example:** `SUM` vs. `SUMX`
```dax
// Simple sum of a column (fast)
Total Sales = SUM(Sales[Amount])

// Calculate Total Revenue (Price * Quantity) for each row, then sum it up.
// You CANNOT do SUM(Sales[Price] * Sales[Quantity]). You must use an iterator.
Total Revenue = SUMX( Sales, Sales[Unit Price] * Sales[Quantity] )
```

##### **B) Filter & Context Functions (Used with `CALCULATE`)**

| Function | Description | Example Use Case |
| :--- | :--- | :--- |
| `FILTER` | Returns a filtered table. Often used inside `CALCULATE`. | `CALCULATE( [Total Sales], FILTER('Products', [Product Color] = "Red") )` |
| `ALL` | **Removes all filters** from a table or specific columns. | `% of Grand Total = DIVIDE( [Total Sales], CALCULATE([Total Sales], ALL(Sales)) )` |
| `ALLEXCEPT` | **Removes all filters** *except* for the columns you specify. | Calculate subtotal for a country, ignoring product filters: `CALCULATE([Total Sales], ALLEXCEPT('Sales', 'Geography'[Country]) )` |
| `REMOVEFILTERS`| Modern syntax for `ALL()`. More readable. | `CALCULATE( [Total Sales], REMOVEFILTERS(Products) )` |
| `KEEPFILTERS` | Modifies the filter context without overriding it (finds the intersection). | Advanced filtering scenarios where you want to combine contexts. |

##### **C) Time Intelligence Functions**

**Prerequisite:** You **MUST** have a well-formed **Date Table** in your model, marked as a date table.

| Function | Description |
| :--- | :--- |
| `TOTALYTD / QTD / MTD` | Calculates the total from the beginning of the year/quarter/month to the current date. |
| `SAMEPERIODLASTYEAR` | Returns a set of dates from the same period in the previous year. Perfect for YoY comparisons. |
| `DATEADD` | Shifts a set of dates by a specified interval (day, month, quarter, year). |
| `DATESINPERIOD` | Returns a table of dates from a start date for a specified interval. |
| `PARALLELPERIOD`| Similar to `DATEADD` but for whole periods. |

**Example: Year-over-Year Sales Growth**
```dax
Sales Last Year = CALCULATE( [Total Sales], SAMEPERIODLASTYEAR('Date'[Date]) )

Sales YoY % = DIVIDE( ([Total Sales] - [Sales Last Year]), [Sales Last Year] )
```

##### **D) Logical & Conditional Functions**

| Function | Description |
| :--- | :--- |
| `IF` | Checks a condition and returns one value if true, another if false. |
| `SWITCH` | A cleaner, more efficient way to handle multiple `IF` conditions. |
| `ISBLANK` | Checks if a value or expression is blank. |
| `COALESCE` | Returns the first expression that does not evaluate to BLANK. |

**Example:** `SWITCH` is better than nested `IF`s.
```dax
Sales Tier =
SWITCH(
    TRUE(),
    [Total Sales] > 10000, "Gold",
    [Total Sales] > 5000, "Silver",
    [Total Sales] > 1000, "Bronze",
    "Basic" // Else condition
)
```

---

#### **4. DAX Best Practices for Performance & Readability**

1.  **Use Variables (`VAR...RETURN`):**
    *   **Improves Readability:** Breaks down complex formulas into logical steps.
    *   **Improves Performance:** A variable is calculated only once and reused.
    *   **Simplifies Debugging:** You can temporarily change the `RETURN` statement to output an intermediate variable to check its value.

    ```dax
    Sales YoY % =
    VAR SalesCurrentYear = [Total Sales]
    VAR SalesPreviousYear = CALCULATE( [Total Sales], SAMEPERIODLASTYEAR('Date'[Date]) )
    VAR Result = DIVIDE( (SalesCurrentYear - SalesPreviousYear), SalesPreviousYear )
    RETURN
        Result
    ```

2.  **Use `DIVIDE()` for Division:** Always use `DIVIDE(numerator, denominator, [alternate_result])` instead of the `/` operator. It automatically handles division-by-zero errors without breaking your visuals.

3.  **Format Your Code:** Use an online tool like **DAX Formatter** or plugins in external tools to make your code neat, indented, and easy to read.

4.  **Create Explicit Measures:** Do not drag raw columns into visuals. Always create a measure first (e.g., `Total Sales = SUM(Sales[Amount])`). This ensures consistent logic across your entire report.

5.  **Comment Your Code:** Use `//` for single-line comments or `/* ... */` for multi-line comments to explain complex logic.
