---

## **Power BI Interview Scenario – Customer Repeat Behavior Using** `**INTERSECT**`

### **Question Statement:**

In this scenario-based question commonly asked in Power BI interviews, you're given:

- A **Sales Fact Table** containing:
    - `Order Date`
    - `Customer Name`
    - `Total Sale`
    - `City`
- A **Calendar Table** linked via `Order Date`.

The interviewer provides a **Date Slicer** and asks for three measures:

1. **Total Customers** who placed orders in the selected date range.
2. **Last Year’s Customers** who placed orders in the _same_ range shifted back by 365 days.
3. **Common Customers** between the selected range and the same range in the prior year (i.e., repeat customers).

---

## **Solution Architecture:**

All measures use the `CALCULATETABLE`, `VALUES`, `DATESBETWEEN`, and `INTERSECT` DAX functions, relying on **row context elimination** and **virtual table construction**.

---

## **Step-by-Step DAX Solutions:**

### **1. Total Customers in Selected Date Range**

```Plain
Total Customers =
COUNTROWS(
    CALCULATETABLE(
        VALUES(Sales[Customer Name]),
        DATESBETWEEN(
            'Calendar'[Date],
            MIN('Calendar'[Date]),
            MAX('Calendar'[Date])
        )
    )
)
```

### **Explanation:**

- `VALUES(Sales[Customer Name])` generates a unique list of customers.
- `CALCULATETABLE` filters this list based on the selected date range.
- `COUNTROWS` gives the number of unique customers in the range.

---

### **2. Last Year’s Customers in the Same Relative Range**

```Plain
Last Year Customers =
COUNTROWS(
    CALCULATETABLE(
        VALUES(Sales[Customer Name]),
        DATESBETWEEN(
            'Calendar'[Date],
            MIN('Calendar'[Date]) - 365,
            MAX('Calendar'[Date]) - 365
        )
    )
)
```

### **Explanation:**

- Same logic as above, but the selected range is offset by **365 days** using date arithmetic.

---

### **3. Common Customers (Repeat Customers)**

```Plain
Common Customers =
COUNTROWS(
    INTERSECT(
        CALCULATETABLE(
            VALUES(Sales[Customer Name]),
            DATESBETWEEN(
                'Calendar'[Date],
                MIN('Calendar'[Date]),
                MAX('Calendar'[Date])
            )
        ),
        CALCULATETABLE(
            VALUES(Sales[Customer Name]),
            DATESBETWEEN(
                'Calendar'[Date],
                MIN('Calendar'[Date]) - 365,
                MAX('Calendar'[Date]) - 365
            )
        )
    )
)
```

### **Explanation:**

- `INTERSECT()` performs a **set intersection** between:
    - Customers in the selected range
    - Customers in the same range last year
- `COUNTROWS()` returns how many customers are common to both — i.e., repeat customers.

---

## **Expected Behavior and Output:**

|   |   |   |   |
|---|---|---|---|
|City|Total Customers|Last Year Customers|Common Customers|
|Ghaziabad|1|1|1|
|Varanasi|2|1|1|
|Gorakhpur|1|0|0|

- Measures dynamically respond to the slicer.
- Shows clear customer retention insights per city.

---

## **Why This is a Strong Interview Use Case:**

- Tests understanding of **virtual tables**, **temporal filtering**, and **set operations**.
- Evaluates your ability to **compare time-bound segments** (period-over-period logic).
- `INTERSECT()` is underused but incredibly powerful for such comparisons.

---

## **Optional Enhancements:**

- Replace hardcoded `365` logic with `SAMEPERIODLASTYEAR()` if the calendar is continuous and marked as a date table.
- Add visuals (bar chart by city) to interpret retention trends.

---

Would you like a dynamic version that compares YoY retention using `SAMEPERIODLASTYEAR()` instead of manual offset?

  

Excellent — using `SAMEPERIODLASTYEAR()` instead of manual `-365` offsets is not only more elegant, but also **calendar-aware**, **fiscal-year-compliant**, and **easier to maintain**.

Below is the **refactored solution** using `SAMEPERIODLASTYEAR()`, assuming your `'Calendar'` table is properly marked as a **Date Table** and is related to `Sales[Order Date]`.

---

## ✅ **Optimized DAX Solution with** `**SAMEPERIODLASTYEAR()**`

---

### **1. Total Customers in Selected Date Range**

```Plain
Total Customers =
COUNTROWS(
    CALCULATETABLE(
        VALUES(Sales[Customer Name]),
        DATESBETWEEN(
            'Calendar'[Date],
            MIN('Calendar'[Date]),
            MAX('Calendar'[Date])
        )
    )
)
```

✅ **No change needed** — this is the correct current-period customer count.

---

### **2. Last Year Customers using** `**SAMEPERIODLASTYEAR()**`

```Plain
Last Year Customers =
COUNTROWS(
    CALCULATETABLE(
        VALUES(Sales[Customer Name]),
        SAMEPERIODLASTYEAR(
            DATESBETWEEN(
                'Calendar'[Date],
                MIN('Calendar'[Date]),
                MAX('Calendar'[Date])
            )
        )
    )
)
```

### 🔍 **Why this is better:**

- `SAMEPERIODLASTYEAR()` **respects fiscal calendars**, holidays, and leap years (unlike manual `365`).
- Cleaner syntax and less error-prone.

---

### **3. Common Customers with Intersect + SAMEPERIODLASTYEAR**

```Plain
Common Customers =
COUNTROWS(
    INTERSECT(
        CALCULATETABLE(
            VALUES(Sales[Customer Name]),
            DATESBETWEEN(
                'Calendar'[Date],
                MIN('Calendar'[Date]),
                MAX('Calendar'[Date])
            )
        ),
        CALCULATETABLE(
            VALUES(Sales[Customer Name]),
            SAMEPERIODLASTYEAR(
                DATESBETWEEN(
                    'Calendar'[Date],
                    MIN('Calendar'[Date]),
                    MAX('Calendar'[Date])
                )
            )
        )
    )
)
```

---

## ✅ **Advantages of This Approach**

|   |   |   |
|---|---|---|
|Feature|`-365 Offset`|`SAMEPERIODLASTYEAR()`|
|Handles leap years|❌|✅|
|Respects fiscal calendars|❌|✅|
|Easier to audit & read|❌|✅|
|Uses standard DAX idioms|⚠️ Often skipped|✅ Encouraged best practice|

---

## 📊 **Visual Suggestion:**

Add a matrix or bar chart by `City` showing:

- `Total Customers`
- `Last Year Customers`
- `Common Customers`

Then add a calculated **% Repeat Customers**:

```Plain
% Repeat Customers =
DIVIDE([Common Customers], [Last Year Customers])
```

---

Would you like to extend this to **Month-over-Month** or **Rolling 12-Month Repeaters** next?