### **Scenario Explained:**

The video demonstrates how to **highlight the highest and lowest values in a Power BI line chart**—a common interview question testing DAX skills. Here’s the breakdown:

---

### **Problem Statement:**

- **Data:** A line chart shows **sales by country** (e.g., France, Germany, India).
- **Goal:** Create measures to **identify and display only the max and min sales values** on the chart, making them visually stand out.

### **Example Data:**

|   |   |
|---|---|
|Country|Sales|
|Germany|1600|
|France|1200|
|South Africa|150|

**Expected Output:**

- **Max point:** Germany (1600)
- **Min point:** South Africa (150)

---

### **Solution: DAX Measures**

### **1. Measure for Maximum Value:**

```Plain
Max Value =
VAR MaxDataPoint = MAXX(ALL('Table'), [Total Sales])  // Finds global max sales
VAR CheckMax =
    IF(
        [Total Sales] = MaxDataPoint,  // Compares current row's sales to max
        MaxDataPoint,                  // Returns max if match
        BLANK()                        // Else returns blank
    )
RETURN CheckMax
```

### **2. Measure for Minimum Value:**

```Plain
Min Value =
VAR MinDataPoint = MINX(ALL('Table'), [Total Sales])  // Finds global min sales
VAR CheckMin =
    IF(
        [Total Sales] = MinDataPoint,  // Compares current row's sales to min
        MinDataPoint,                  // Returns min if match
        BLANK()                        // Else returns blank
    )
RETURN CheckMin
```

---

### **Key Steps Explained:**

1. `**MAXX**`**/**`**MINX**` **with** `**ALL**`**:**
    - `ALL('Table')` ignores filters to calculate the **global max/min** across all countries.
    - `MAXX`/`MINX` iterates through the table to find the extreme values.
2. `**IF**` **Logic:**
    - Compares each row’s sales (`[Total Sales]`) to the global max/min.
    - Returns the value **only if it matches** the extreme; otherwise, returns `BLANK()`.
3. **Visual Impact:**
    - When added to the line chart, these measures **display only the max/min points**, hiding other values with `BLANK()`.

---

### **Why Not Use** `**MAX**`**/**`**MIN**` **Directly?**

- `MAX([Total Sales])` would **not respect row context**—it returns the same value for all rows.
- `MAXX`/`MINX` iterate row-by-row, allowing dynamic comparison.

---

### **Pro Tip: Use Conditional Formatting**

For better visualization:

1. Add the measures to the line chart’s **tooltip** or **legend**.
2. Use **conditional formatting** to color max/min points differently (e.g., green for max, red for min).

---

### **Interview Takeaways:**

1. **Understand context transition:** `MAXX`/`MINX` work with row context; `MAX`/`MIN` don’t.
2. **Use** `**ALL**` **to ignore filters** for global calculations.
3. **Leverage** `**BLANK()**` to hide non-extreme values in visuals.

This pattern is reusable for scenarios like:

- Highlighting top/bottom performers.
- Flagging outliers in scatter plots.

Would you like a variation (e.g., highlighting top N values)?