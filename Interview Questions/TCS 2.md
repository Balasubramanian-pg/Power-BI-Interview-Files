### **Power BI Interview Question: Retrieving Previous Row Values in DAX**

---

### **Question**

_How can you create a column that displays the **previous row's Transaction Date** for each record in a Power BI table?_

**Given Data Structure:**

|   |   |
|---|---|
|ID|Transaction Date|
|1|2024-08-01|
|2|2024-08-03|
|3|2024-08-05|

**Expected Output:**

|   |   |   |
|---|---|---|
|ID|Transaction Date|Result (Previous Date)|
|1|2024-08-01|_blank_|
|2|2024-08-03|2024-08-01|
|3|2024-08-05|2024-08-03|

---

### **Solution**

Use a **calculated column** with `MAXX` + `FILTER` + `EARLIER` to reference the prior row's value.

### **DAX Code:**

```Plain
Result Column =
MAXX(
    FILTER(
        'Data',
        'Data'[ID] = EARLIER('Data'[ID]) - 1
    ),
    'Data'[Transaction Date]
)
```

---

### **Code Explanation**

1. `**EARLIER('Data'[ID])**`
    - Retrieves the **current row's ID** during iteration.
    - For ID=2, returns `2`; for ID=3, returns `3`.
2. `**'Data'[ID] = EARLIER(...) - 1**`
    - Filters the table to find the row where `ID = Current ID - 1`.
    - Example: For ID=2, filters rows where `ID=1`.
3. `**MAXX(..., 'Data'[Transaction Date])**`
    - Returns the **Transaction Date** from the filtered row (previous ID).
    - If no match (e.g., ID=1), returns blank.

---

### **Why This Works**

- `**EARLIER**` preserves the row context from the outer calculation.
- `**FILTER**` dynamically isolates the previous row.
- `**MAXX**` extracts the scalar value (date) from the filtered table.

**Alternative:**

`MINX` works identically here since only one row matches the filter.

---

### **Key Takeaways**

✅ Use `EARLIER` to reference the current row in nested contexts.

✅ `MAXX`/`MINX` iterators convert filtered tables to scalar values.

✅ Ideal for lag/lead calculations in time series or ordered data.

---

### **Why** `**MAXX**` **and** `**MINX**` **Give the Same Result in This Scenario**

### **Short Answer**

Because the `FILTER` condition (`'Data'[ID] = EARLIER('Data'[ID]) - 1`) **always returns a single row** (or none). Since there’s only one value to evaluate, both `MAXX` (maximum) and `MINX` (minimum) return that same value.

---

### **Detailed Explanation**

### **1. How the** `**FILTER**` **Works**

The `FILTER` function reduces the table to **only the row where:**

```Plain
'Data'[ID] = EARLIER('Data'[ID]) - 1
```

- For **ID=2**, it finds the row where `ID=1`.
- For **ID=3**, it finds the row where `ID=2`.
- For **ID=1**, no row matches (`0` doesn’t exist), so `FILTER` returns an **empty table**.

### **2.** `**MAXX**` **vs.** `**MINX**` **on a Single-Row Table**

- When `FILTER` returns **one row**, both functions behave identically:
    - `MAXX({Row}, [Transaction Date])` → Returns the date from that row.
    - `MINX({Row}, [Transaction Date])` → Also returns the date from that row.
- When `FILTER` returns **no rows** (e.g., for ID=1):
    - Both return **blank** (no value to maximize/minimize).

### **3. Key Insight**

`MAXX`/`MINX` only differ when applied to **multiple values**. Here, the logic ensures **at most one row** matches, making them interchangeable.

---

### **When Would They Differ?**

If the `FILTER` condition could return **multiple rows**, e.g.:

```Plain
FILTER('Data', 'Data'[ID] <= EARLIER('Data'[ID]) - 1)
```

- For ID=3, this might return rows with `ID=1` and `ID=2`.
- `MAXX` would return the **latest date** (max).
- `MINX` would return the **earliest date** (min).

---

### **Practical Takeaway**

✅ In **single-row contexts**, `MAXX`/`MINX` (or `FIRST`/`LAST`) are equivalent.

✅ Use `MAXX`/`MINX` intentionally when:

- Your filter logic might return multiple rows.
- You need to enforce a priority (e.g., "latest previous date").

---

### **Example Where** `**MAXX**` **and** `**MINX**` **Produce Different Results**

### **Scenario: Tracking Multiple Transactions per Customer**

**Problem:**

You have a sales table where **customers can have multiple transactions on different dates**, and you want to find:

1. Their **most recent** prior transaction date (`MAXX`).
2. Their **earliest** prior transaction date (`MINX`).

---

### **Sample Data**

|   |   |   |
|---|---|---|
|CustomerID|OrderID|TransactionDate|
|1|101|2024-01-05|
|1|102|2024-01-10|
|1|103|2024-01-15|
|2|201|2024-02-01|

---

### **DAX Measures**

### **1. Last Prior Transaction Date (**`**MAXX**`**)**

```Plain
Last Prior Date =
MAXX(
    FILTER(
        Sales,
        Sales[CustomerID] = EARLIER(Sales[CustomerID]) &&  // Same customer
        Sales[TransactionDate] < EARLIER(Sales[TransactionDate])  // Earlier date
    ),
    Sales[TransactionDate]
)
```

**Output Logic:**

- For **OrderID 103** (Customer 1, 2024-01-15):
    - Filters to Customer 1’s earlier transactions (101 and 102).
    - `MAXX` returns **2024-01-10** (most recent prior date).

### **2. First Prior Transaction Date (**`**MINX**`**)**

```Plain
First Prior Date =
MINX(
    FILTER(
        Sales,
        Sales[CustomerID] = EARLIER(Sales[CustomerID]) &&  // Same customer
        Sales[TransactionDate] < EARLIER(Sales[TransactionDate])  // Earlier date
    ),
    Sales[TransactionDate]
)
```

**Output Logic:**

- For **OrderID 103** (Customer 1, 2024-01-15):
    - Same filtered rows (101 and 102).
    - `MINX` returns **2024-01-05** (earliest prior date).

---

### **Result Comparison**

|   |   |   |   |
|---|---|---|---|
|OrderID|TransactionDate|Last Prior Date (`MAXX`)|First Prior Date (`MINX`)|
|101|2024-01-05|_blank_|_blank_|
|102|2024-01-10|2024-01-05|2024-01-05|
|103|2024-01-15|2024-01-10|2024-01-05|
|201|2024-02-01|_blank_|_blank_|

### **Key Differences:**

- `MAXX` returns the **closest/latest** prior date.
- `MINX` returns the **oldest/first** prior date.
- Both return _blank_ if no prior transactions exist.

---

### **When to Use Which?**

- `**MAXX**`: Useful for "last interaction" analysis (e.g., customer’s most recent purchase before this one).
- `**MINX**`: Helpful for "first interaction" tracking (e.g., a customer’s initial purchase date in a sequence).

**Real-World Example:**

- A subscription service might use `MAXX` to find the **last renewal date** and `MINX` to identify the **signup date**.

---

### **Scalar vs. Vector Values in Power BI (DAX Context)**

In Power BI (and DAX), the key difference between scalar and vector values lies in **dimensionality** and **how they're used in calculations**.

---

## **1. Scalar Value (Single Value)**

- **Definition**: A **single** constant or computed value (1x1 dimension).
- **Examples**:
    - `5` (number)
    - `"Power BI"` (text)
    - `SUM(Sales[Revenue])` (aggregated measure)
    - `TODAY()` (date function)

### **Key Characteristics**:

✅ **Single output**: Always returns **one value**.

✅ **Used in**: Measures, calculated columns, comparisons.

✅ **Example DAX**:

```Plain
Total Sales = SUM(Sales[Amount])  // Returns a single number (e.g., $10,000)
```

---

## **2. Vector Value (Column/Table of Values)**

- **Definition**: A **list/column** of values (Nx1 dimension).
- **Examples**:
    - `Sales[Product]` (all product names)
    - `FILTER(Sales, Sales[Qty] > 100)` (table of filtered rows)
    - `VALUES(Customers[City])` (unique cities)

### **Key Characteristics**:

✅ **Multiple values**: Returns **a set of data points**.

✅ **Used in**: Iterators (`SUMX`, `AVERAGEX`), filtering, `FILTER()` functions.

✅ **Example DAX**:

```Plain
Product List = VALUES(Products[Name])  // Returns all product names as a column
```

---

## **Key Differences**

|   |   |   |
|---|---|---|
|Feature|**Scalar**|**Vector**|
|**Output**|Single value|Column/table of values|
|**DAX Context**|Used in measures|Used in iterators (`X` functions)|
|**Example**|`SUM([Sales])`|`Sales[Product]`|
|**Storage**|Stored as a result|References a column|

---

## **Practical Implications**

1. **Scalar in Measures**:
    
    ```Plain
    Total Profit = [Total Revenue] - [Total Cost]  // Subtracts two scalars
    ```
    
2. **Vector in Iterators**:
    
    ```Plain
    Avg Sales = AVERAGEX(Sales, Sales[Amount])  // Iterates over a vector (Sales[Amount])
    ```
    
3. **Error Prevention**:
    - **Scalar expected, but vector provided**:
        
        ```Plain
        // WRONG: SUM() expects a column, but FILTER returns a table.
        SUM(FILTER(Sales, Sales[Qty] > 100))
        ```
        
    - **Fix**: Wrap with `SUMX`:
        
        ```Plain
        SUMX(FILTER(Sales, Sales[Qty] > 100), Sales[Amount])  // Correct
        ```
        

---

## **When to Use Which?**

- **Scalar**: For final calculations (KPIs, metrics).
- **Vector**: For row-by-row operations (`X` functions, filtering).