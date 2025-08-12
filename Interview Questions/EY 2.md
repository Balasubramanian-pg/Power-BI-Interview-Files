---
Pattern:
  - Parallel Period
---
### **Scenario Explained:**

This Power BI interview question focuses on **time intelligence functions** (`DATEADD` vs. `PARALLELPERIOD`) and the difference between `VALUES()` and `DISTINCT()` functions. Here's the breakdown:

---

## **Part 1:** `**DATEADD**` **vs.** `**PARALLELPERIOD**`

### **Problem Statement:**

- **Data:** A table with `Year`, `Month`, and `Total Sales` (e.g., 2020-Jan: 100, 2020-Feb: 150, 2021-Jan: 200, etc.).
- **Goal:** Calculate **last year’s sales** for each month (e.g., Jan 2021 should show Jan 2020 sales).

### **Solution:**

### **1. Using** `**DATEADD**` **(Returns exact month comparison)**

```Plain
Last Year Sales (DATEADD) =
CALCULATE(
    [Total Sales],
    DATEADD('Calendar'[Date], -1, YEAR)  // Shifts dates back by 1 year
)
```

**Output:**

|   |   |   |   |
|---|---|---|---|
|Year|Month|Total Sales|Last Year Sales (DATEADD)|
|2021|Jan|200|**100** (Jan 2020)|
|2021|Feb|300|**150** (Feb 2020)|

✅ **Pros:**

- Compares **the same month** in the previous year.
- Works for **monthly, quarterly, or daily** comparisons.

---

### **2. Using** `**PARALLELPERIOD**` **(Returns aggregated period)**

```Plain
Last Year Sales (PARALLELPERIOD) =
CALCULATE(
    [Total Sales],
    PARALLELPERIOD('Calendar'[Date], -1, YEAR)  // Aggregates entire previous year
)
```

**Output:**

|   |   |   |   |
|---|---|---|---|
|Year|Month|Total Sales|Last Year Sales (PARALLELPERIOD)|
|2021|Jan|200|**250** (Sum of Jan+Feb 2020)|
|2021|Feb|300|**250** (Sum of Jan+Feb 2020)|

⚠ **Key Difference:**

- `PARALLELPERIOD` **sums all values** in the previous year (not month-to-month).
- Useful for **YTD comparisons** but **not** for exact monthly comparisons.

---

## **Part 2:** `**VALUES()**` **vs.** `**DISTINCT()**`

### **Problem Statement:**

- Both functions return **unique values** from a column, but they handle **blanks** differently.

### **Solution:**

### **1.** `**VALUES()**` **(Includes** `**BLANK**` **as a distinct value)**

```Plain
Unique Customers (VALUES) = COUNTROWS(VALUES('Sales'[Customer]))
```

- If `Customer` column has: `["Alice", "Bob", BLANK]` → Returns **3**.

### **2.** `**DISTINCT()**` **(Ignores** `**BLANK**`**)**

```Plain
Unique Customers (DISTINCT) = COUNTROWS(DISTINCT('Sales'[Customer]))
```

- Same data → Returns **2** (ignores `BLANK`).

✅ **When to Use Which:**

- Use `VALUES()` when blanks are meaningful (e.g., "Unknown" customers).
- Use `DISTINCT()` when blanks should be excluded.

---

### **Interview Takeaways:**

1. `**DATEADD**` **vs.** `**PARALLELPERIOD**`**:**
    - `DATEADD` = Exact period shift (e.g., same month last year).
    - `PARALLELPERIOD` = Aggregates entire shifted period (e.g., full previous year).
2. `**VALUES()**` **vs.** `**DISTINCT()**`**:**
    - `VALUES()` includes blanks; `DISTINCT()` excludes them.
3. **Common Use Cases:**
    - `DATEADD`: Monthly/quarterly comparisons.
    - `PARALLELPERIOD`: YTD or annual trends.

Would you like a practical example applying both concepts?