### **Scenario Explained:**

This Power BI interview question tests your ability to **restrict Year-to-Date (YTD) calculations to the last order date** in a dataset. Here’s the breakdown:

---

### **Problem Statement:**

- **Data:**
    - A sales table with an `Order Date` column (latest date: July 2, 2022).
    - A calendar table for time intelligence.
- **Issue:**
    - A **YTD measure** (`Total YTD`) shows values for **all dates in July 2022**, even though the last order was on July 2.
    - **Goal:** Modify the YTD measure to **stop calculating after the last order date**.

### **Example:**

|   |   |   |   |
|---|---|---|---|
|Date|Total Sales|Original YTD|Expected YTD (Restricted)|
|Jul 1, 2022|1000|1000|1000|
|Jul 2, 2022|1500|2500|2500|
|Jul 3, 2022|0|2500|**BLANK** (No orders)|

---

### **Solution: Use** `**TOTALYTD**` **with a Filter**

### **Step 1: Find the Last Order Date**

```Plain
MaxOrderDate = MAX('Sales'[Order Date])  // Returns July 2, 2022
```

### **Step 2: Restrict YTD to Dates ≤ Last Order Date**

```Plain
Total YTD Restricted =
VAR MaxOrderDate = MAX('Sales'[Order Date])
RETURN
    TOTALYTD(
        [Total Sales],
        'Calendar'[Date],
        'Calendar'[Date] <= MaxOrderDate  // Optional filter argument
    )
```

---

### **Key Points:**

1. `**TOTALYTD**` **Syntax:**
    
    ```Plain
    TOTALYTD(<expression>, <dates>, [<filter>], [<year_end_date>])
    ```
    
    - The **third argument (**`**filter**`**)** restricts the calculation to specific dates.
2. **Why It Works:**
    - Without the filter, `TOTALYTD` calculates for **all dates in the year**, even if no orders exist.
    - The filter ensures YTD stops at the **last valid order date**.
3. **Visual Impact:**
    - Dates **after July 2** show `BLANK` (or zero) instead of repeating the last YTD value.

---

### **Alternative: Using** `**DATESYTD**` **+** `**FILTER**`

```Plain
Total YTD Restricted =
VAR MaxOrderDate = MAX('Sales'[Order Date])
VAR YTDDates =
    FILTER(
        DATESYTD('Calendar'[Date]),
        'Calendar'[Date] <= MaxOrderDate
    )
RETURN
    CALCULATE([Total Sales], YTDDates)
```

---

### **Interview Takeaways:**

1. `**TOTALYTD**` **Default Behavior:**
    - Calculates for **all dates in the year**, which may not match business logic.
2. **Dynamic Restriction:**
    - Use `MAX(OrderDate)` to dynamically limit calculations.
3. **Real-World Use Cases:**
    - Financial reporting (e.g., YTD revenue up to the current quarter).
    - Inventory management (stop calculations at last transaction date).

**Pro Tip:** For **Month-to-Date (MTD)**, use:

```Plain
Total MTD Restricted =
VAR MaxOrderDate = MAX('Sales'[Order Date])
RETURN
    TOTALMTD(
        [Total Sales],
        'Calendar'[Date],
        'Calendar'[Date] <= MaxOrderDate
    )
```

Would you like a variation (e.g., handling fiscal years)?