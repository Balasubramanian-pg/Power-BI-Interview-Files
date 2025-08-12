### 1. Total = a * b / c * d how do you optimize this dax?

This question is about writing robust, clear, and efficient DAX code. The "optimization" here has two parts: preventing errors and improving readability/performance.

The original formula `Total = a * b / c * d` is ambiguous. Due to DAX's left-to-right operator precedence, it would be calculated as `((a * b) / c) * d`. However, the main risk is a "division by zero" error if `c` is zero or blank.

**The best practice is to use the** `**DIVIDE()**` **function.**

Here is the optimized DAX, also using variables for clarity and efficiency:

```Plain
Total Optimized =
VAR Numerator = [a] * [b] * [d]
VAR Denominator = [c]
VAR Result =
    DIVIDE ( Numerator, Denominator, 0 ) // The 0 is the alternate result if division by zero occurs.
RETURN
    Result
```

**Why this is better:**

1. **Error Handling:** `DIVIDE(x, y)` automatically handles cases where `y` is zero or BLANK, returning the specified alternate result (or BLANK if not specified). Using the `/` operator would throw an error and break your visual.
2. **Readability:** Using `VAR` (variables) breaks the calculation into logical steps. It's much easier to read and debug what the numerator and denominator are.
3. **Performance:** DAX calculates variables only once. If `[a]`, `[b]`, `[c]`, or `[d]` were complex measures themselves, using variables would prevent them from being recalculated multiple times within the same formula.

---

### 2. Difference between star schema and snowflake schema

These are two common data modeling designs used in data warehousing and business intelligence.

|   |   |   |
|---|---|---|
|Feature|Star Schema|Snowflake Schema|
|**Structure**|A central **Fact table** is connected directly to multiple **Dimension tables**. It looks like a star.|A central **Fact table** is connected to Dimension tables, which are **normalized** and can be connected to other dimension tables. It looks like a snowflake.|
|**Normalization**|Dimensions are **denormalized**. This means they contain redundant data to avoid complex joins.|Dimensions are **normalized**. Redundant data is removed and put into separate tables.|
|**Query Complexity**|Simpler and faster queries. Fewer joins are needed to get the data.|More complex queries. Multiple joins are often required to link through the normalized dimensions.|
|**Performance in Power BI**|**Highly recommended**. The VertiPaq engine in Power BI is optimized for this structure, leading to faster performance and lower memory usage.|**Not recommended** for Power BI. The extra joins between dimension tables can significantly slow down report performance.|
|**Data Redundancy**|Higher data redundancy, leading to a slightly larger storage footprint for the dimensions.|Lower data redundancy, which can make dimension maintenance easier.|

**In summary:** For Power BI, the **Star Schema is the gold standard**. You should always aim to transform a snowflake schema into a star schema in Power Query before loading it into the model.

---

### 3. What is data flows in power bi?

Power BI Dataflows are a **cloud-based, self-service data preparation** feature. Think of them as **"Power Query in the cloud."**

**Key Characteristics:**

- **Reusable ETL:** You create ETL (Extract, Transform, Load) logic using the familiar Power Query interface, but you do it in the Power BI Service instead of Power BI Desktop.
- **Centralized Data:** The output of a dataflow is a set of tables (called entities) stored in Azure Data Lake Storage Gen2 within your organization's Power BI tenant.
- **Single Source of Truth:** Multiple Power BI datasets (and their corresponding reports) can connect to the same dataflow. This ensures that everyone is using the same cleansed, transformed, and standardized data (e.g., a "Golden Customer Table" or "Official Product List").
- **Separation of Duties:** It separates the data preparation process (done in dataflows) from the data modeling and report creation process (done in Power BI Desktop).

---

### 4. Different refreshes in Power BI

The type of refresh depends on the **Data Connection Mode** (Import, DirectQuery, Live Connection).

**For Import Mode:** (Data is copied into the Power BI file)

1. **Manual Refresh:** Also called "On-demand refresh." You go to the Power BI Service and click the "Refresh now" button for a dataset.
2. **Scheduled Refresh:** You configure a schedule in the Power BI Service to automatically refresh your dataset.
    - **Power BI Pro:** Up to 8 times per day.
    - **Power BI Premium/PPU:** Up to 48 times per day.
3. **Incremental Refresh (Premium Feature):** For very large datasets, this feature intelligently partitions the data and only refreshes the most recent partitions (e.g., only refresh the last day's worth of data instead of all 5 years). This is much faster and more efficient.

**For DirectQuery / Live Connection Mode:** (No data is imported; queries are sent to the source)

- There is no "data refresh" in the same sense as Import mode. Visuals query the data source in real-time.
- **Automatic Page Refresh (Configurable):** You can set a report page to automatically refresh all its visuals at a specific interval (e.g., every 5 minutes), which re-sends the queries to the source.

---

### 5. Challenges you have faced during development of reports

This is a common interview question. A good answer shows you can identify problems and, more importantly, solve them.

Here are some common challenges and solutions:

1. **Poor Data Quality:**
    - **Challenge:** The source data is messy, with nulls, incorrect data types, and inconsistent formatting.
    - **Solution:** I use **Power Query** extensively for data cleansing. I profile the data to identify issues, use transformations to replace values, change data types, split columns, and unpivot data to create a clean and tidy model.
2. **Slow Report Performance:**
    - **Challenge:** Visuals take a long time to load, or slicers are slow to respond.
    - **Solution:** I use **Performance Analyzer** in Power BI Desktop to identify the bottleneck (slow visual, slow DAX query, or slow source query). Then I take action, such as:
        - Optimizing the data model (using a star schema).
        - Rewriting DAX measures to be more efficient (using variables, `DIVIDE()`, etc.).
        - Reducing the number of visuals on a page or reducing data cardinality.
3. **Vague or Changing Requirements:**
    - **Challenge:** The business user is not sure what they want, or the requirements change halfway through the project.
    - **Solution:** I use an agile approach. I start by building a **mockup or a simple prototype** to get early feedback. I have frequent check-ins with stakeholders to ensure the report is aligned with their needs, which helps manage scope creep and deliver a more valuable final product.
4. **Complex DAX Calculations:**
    - **Challenge:** The business logic requires a very complex calculation, like semi-additive measures or complex time intelligence.
    - **Solution:** I break the problem down into smaller parts using DAX variables. I test each part individually. I also use tools like **DAX Studio** to analyze the query plans and performance of my measures, and I frequently consult with online communities and documentation for best practices.

---

### 6. What are the field parameters?

**Field Parameters** are a feature in Power BI that allows users to **dynamically change the measures or dimensions** used in a visual.

It creates a slicer or filter that lets the report consumer choose what data they want to see.

**Example Use Cases:**

- **Dynamic Measures:** Create one bar chart and let the user switch between viewing `[Total Sales]`, `[Total Profit]`, and `[Total Quantity]` using a slicer. This avoids having to create three separate charts.
- **Dynamic Dimensions:** Let the user change the X-axis of a chart to analyze data by `Product Category`, `Region`, or `Customer Segment`.

This feature greatly enhances interactivity and reduces the number of visuals needed on a report page.

---

### 7. How to keep the default total sales values even if the external users apply filters?

This is a classic DAX scenario that requires modifying the filter context. The key is to use the `CALCULATE` function combined with a filter-removing function like `ALL`, `ALLEXCEPT`, or `REMOVEFILTERS`.

**The Solution:**

You need to create a new measure that ignores specific filters.

**Scenario 1: Ignore ALL filters on the report**

```Plain
Total Sales (All Filters Removed) =
CALCULATE (
    [Your Original Total Sales Measure],
    REMOVEFILTERS () // or ALL('YourFactTable')
)
```

**Scenario 2: Ignore filters from a specific table (e.g., the 'Product' table)**

If a user filters by a product, you still want to show the grand total sales for all products.

```Plain
Total Sales (Ignoring Product Filter) =
CALCULATE (
    [Your Original Total Sales Measure],
    REMOVEFILTERS ( 'Product' ) // or ALL('Product')
)
```

**Scenario 3: Ignore all filters EXCEPT one (e.g., keep the Date filter but ignore others)**

```Plain
Total Sales (Ignoring All But Date) =
CALCULATE (
    [Your Original Total Sales Measure],
    ALLEXCEPT ( 'FactSales', 'Date'[Date] )
)
```

This is very powerful for calculating things like "% of Grand Total."

---

### 8. rls

**RLS** stands for **Row-Level Security**.

It is a Power BI feature that restricts data access for different users at the data row level. This ensures that users can only see the data they are authorized to see.

**How it works:**

1. **Define Roles:** In Power BI Desktop, you create "roles" (e.g., "Sales Manager - North America", "Sales Rep").
2. **Apply DAX Filters:** For each role, you write a DAX filter expression on a table. For example, for the "North America" role, the filter on the `Sales` table might be `[Region] = "North America"`.
3. **Map Users in Service:** After publishing the report to the Power BI Service, an admin maps users or security groups to these roles.

When a user from that group views the report, the RLS filter is automatically applied, and they only see data for North America.

There are two types:

- **Static RLS:** You create a separate role for each entity you want to filter (e.g., a role for each country).
- **Dynamic RLS:** A more scalable approach where you use a single role with a DAX function like `USERPRINCIPALNAME()` to look up the user's email in a security table and apply the appropriate filters automatically.

---

### 9. I have a category column with a,b,c,d. I wanted to show visual with x axis c,d,a,b. How to handle this scenario?

This is a **custom sorting** problem. By default, Power BI will sort the text column `[Category]` alphabetically (a, b, c, d). To define a custom order, you need to use the **Sort by Column** feature.

**Here is the step-by-step solution:**

**Step 1: Create a Sorting Column**  
You need to create a new column that defines the numerical order you want. You can do this in Power Query or with a DAX calculated column. Let's call it `CategorySortOrder`.

In Power Query (recommended) or as a DAX Calculated Column:

```Plain
CategorySortOrder =
SWITCH (
    TRUE (),
    'YourTable'[Category] = "c", 1,
    'YourTable'[Category] = "d", 2,
    'YourTable'[Category] = "a", 3,
    'YourTable'[Category] = "b", 4,
    99 // A default for any other categories
)
```

Your table will now look like this:

|   |   |
|---|---|
|Category|CategorySortOrder|
|a|3|
|b|4|
|c|1|
|d|2|

**Step 2: Apply the "Sort by Column" Feature**

1. Go to the **Data View** in Power BI Desktop.
2. Select the `**Category**` column (the column you want to sort).
3. Go to the **Column tools** tab in the top ribbon.
4. Click on the **Sort by Column** button.
5. From the dropdown list, select your new sorting column: `**CategorySortOrder**`.

That's it! Now, any visual in your report that uses the `Category` column will automatically sort it in the order you defined: c, d, a, b.