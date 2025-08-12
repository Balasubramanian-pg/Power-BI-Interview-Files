---
Pattern:
  - Summarize
---
## Problem Statement

**Given:** A transaction table with:

- Date
- Transaction Type 1
- Transaction Type 2

**Requirement:** Create a new table showing:

1. Date
2. Unique transaction types (from both columns)
3. Count of each transaction type

### Sample Input

|   |   |   |
|---|---|---|
|Date|Transaction Type 1|Transaction Type 2|
|2024-01-01|A|B|
|2024-01-01|A|C|
|2024-01-01|B|D|
|2024-01-01|C|E|
|2024-01-01|D|A|

### Solution Approach

1. **Create Summary Tables:**
    
    ```Plain
    // For Transaction Type 1
    Var a =
    SUMMARIZE(
        Sheet1,
        Sheet1[Date],
        Sheet1[Transaction Type 1]
    )
    
    // For Transaction Type 2
    Var b =
    SUMMARIZE(
        Sheet1,
        Sheet1[Date],
        Sheet1[Transaction Type 2]
    )
    ```
    
2. **Combine Results:**
    
    ```Plain
    Var c = UNION(a, b)
    ```
    
3. **Create Final Table:**
    
    ```Plain
    Result =
    SELECTCOLUMNS(
        c,
        "Date", [Date],
        "Transaction Type",
            IF(
                CONTAINS(a, [Date], [Transaction Type 1]),
                [Transaction Type 1],
                [Transaction Type 2]
            )
    )
    ```
    
4. **Add Count Measure:**
    
    ```Plain
    Transaction Count =
    COUNTROWS(
        FILTER(
            Result,
            Result[Transaction Type] = SELECTEDVALUE(Result[Transaction Type])
        )
    )
    ```
    

### Expected Output

|   |   |   |
|---|---|---|
|Date|Transaction Type|Count|
|2024-01-01|A|3|
|2024-01-01|B|2|
|2024-01-01|C|2|
|2024-01-01|D|2|
|2024-01-01|E|1|

### Key Insights

1. **SUMMARIZE** creates distinct groupings by date and transaction type
2. **UNION** combines results while preserving duplicates (needed for accurate counting)
3. The count measure uses filtering to calculate occurrences per type

### Alternative Solution

For Power Query users:

1. Unpivot both transaction type columns
2. Group by Date and Transaction Type
3. Count rows per group

This solution demonstrates advanced DAX skills in:

- Table manipulation
- Handling multi-column analysis
- Creating aggregated views from transactional data

  

Here's the detailed solution to the problem:

### Problem Description

Given a table with columns Date, Transaction Type 1, and Transaction Type 2, create a new table that displays:

1. The Date
2. Unique entries of all transaction types from both Transaction Type columns
3. The respective count for each unique transaction type per date

### Solution Approach

We'll follow these steps to solve the problem in Power BI using DAX:

1. Create two temporary tables: one with Date and Transaction Type 1, and another with Date and Transaction Type 2.
2. Combine these two tables using UNION to get all transaction types from both columns.
3. Group the combined data by Date and Transaction Type, counting the occurrences of each transaction type per date.
4. Format the Date column for display.
5. Create a measure to count distinct transaction types (optional).
6. Display the results in a table visual.

### DAX Implementation

Here's the complete DAX code to create the result table:

```Plain
 Copy
result =
VAR Combined =
    UNION(
        SELECTCOLUMNS(
            Sheet1,
            "Date", Sheet1[Date],
            "TransactionType", Sheet1[Transaction Type 1]
        ),
        SELECTCOLUMNS(
            Sheet1,
            "Date", Sheet1[Date],
            "TransactionType", Sheet1[Transaction Type 2]
        )
    )
VAR Grouped =
    GROUPBY(
        Combined,
        [Date],
        [TransactionType],
        "Count", COUNTX(CURRENTGROUP(), [TransactionType])
    )
RETURN
SELECTCOLUMNS(
    Grouped,
    "Date", [Date],
    "FormattedDate", FORMAT([Date], "DD/MM/YYYY"),
    "TransactionType", [TransactionType],
    "Count", [Count]
)
```

And here's the DAX code for the "trans" measure that counts distinct transaction types:

```Plain
 Copy
trans =
DISTINCTCOUNT(result[TransactionType])
```

### Explanation

1. **Combining Data**:
    - We create two tables from the original data: one containing Date and Transaction Type 1, and another containing Date and Transaction Type 2.
    - These two tables are combined using the UNION function, which appends the rows of the second table to the first.
2. **Grouping and Counting**:
    - The GROUPBY function groups the combined data by Date and TransactionType.
    - For each group, it counts the occurrences of the TransactionType using COUNTX.
3. **Date Formatting**:
    - We add a formatted date column using the FORMAT function to display dates in DD/MM/YYYY format.
    - Note that grouping is done on the original date to ensure correct aggregation, while the formatted date is only for display.
4. **Measure for Distinct Counts**:
    - The "trans" measure counts the distinct transaction types across all dates using DISTINCTCOUNT.
5. **Visualization**:
    - Create a table visual in Power BI.
    - Add the FormattedDate (or Date), TransactionType, and Count columns from the "result" table to the visual.
    - This will display the date, each unique transaction type, and its count per date.

### Example

Given the following input data:

|   |   |   |
|---|---|---|
|Date|Transaction Type 1|Transaction Type 2|
|2024-01-01|A|B|
|2024-01-01|C|D|
|2024-01-01|E|A|
|2024-01-01|B|C|
|2024-01-01|D|E|
|2024-01-02|A|A|
|2024-01-02|B|B|
|2024-01-02|C|C|

The output would be:

|   |   |   |
|---|---|---|
|FormattedDate|TransactionType|Count|
|01/01/2024|A|2|
|01/01/2024|B|2|
|01/01/2024|C|2|
|01/01/2024|D|2|
|01/01/2024|E|2|
|02/01/2024|A|2|
|02/01/2024|B|2|
|02/01/2024|C|2|

### Alternative Implementation Using GENERATE

Here's an alternative approach using the GENERATE function to expand the data before combining:

```Plain

result =
VAR Type1 =
    GENERATE(
        Sheet1,
        ROW("TransactionType", Sheet1[Transaction Type 1])
    )
VAR Type2 =
    GENERATE(
        Sheet1,
        ROW("TransactionType", Sheet1[Transaction Type 2])
    )
VAR Combined =
    UNION(
        SELECTCOLUMNS(Type1, "Date", Sheet1[Date], "TransactionType", [TransactionType]),
        SELECTCOLUMNS(Type2, "Date", Sheet1[Date], "TransactionType", [TransactionType])
    )
RETURN
GROUPBY(
    Combined,
    [Date],
    [TransactionType],
    "Count", COUNTX(CURRENTGROUP(), [TransactionType])
)
```

This approach is similar but uses GENERATE to create intermediate tables with expanded rows for each transaction type.

### Final Thoughts

This solution efficiently combines the transaction types from both columns, groups by date and transaction type, and counts the occurrences. The use of UNION to combine the two sets of transaction types is the key insight here, allowing us to treat both columns uniformly in the subsequent grouping and counting steps. The GROUPBY function then neatly handles the aggregation to produce the desired counts per date and transaction type.