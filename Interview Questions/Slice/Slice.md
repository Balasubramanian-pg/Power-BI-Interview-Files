### **Power BI Report Analysis: Key Measures & Visualizations**

---

## **Question 1: How to Calculate Top 5 Products' Profit Share?**

### **Illustrative Example**

|   |   |   |
|---|---|---|
|Product ID|Total Cost|Total Revenue|
|P1|$500|$800|
|P2|$300|$600|
|...|...|...|
|P10|$200|$400|

**Goal:**

Find the **Top 5 products by revenue** and calculate their **profit share (%)** of total cost.

---

### **Solution: DAX Measure**

```Plain
Top 5 Profit Share =
VAR Top5Products = TOPN(5, SUMMARIZE('Sales', 'Sales'[Product ID], "Revenue", SUM('Sales'[Total Revenue]) , [Revenue], DESC)
VAR TotalCost_Top5 = CALCULATE(SUM('Sales'[Total Cost]), Top5Products)
VAR TotalRevenue_Top5 = CALCULATE(SUM('Sales'[Total Revenue]), Top5Products)
VAR TotalCost_Overall = SUM('Sales'[Total Cost])
RETURN
DIVIDE(TotalRevenue_Top5 - TotalCost_Top5, TotalCost_Overall, 0)
```

### **Code Explanation**

1. `**TOPN(5, ...)**` → Filters the top 5 products by revenue.
2. `**SUMMARIZE()**` → Creates a temporary table with `Product ID` and `Revenue`.
3. `**CALCULATE(SUM(...))**` → Computes cost & revenue for the top 5.
4. `**DIVIDE(Profit, TotalCost, 0)**` → Calculates **profit share (%)**.

### **Expected Output**

- If **Top 5 profit = $1,000** and **Total cost = $5,000**, the measure returns **20%**.
- Displayed in a **Card Visual** (e.g., "18.37% Profit Share").

---

## **Question 2: How to Alternate Bar Colors in a Chart?**

### **Illustrative Example**

|   |   |
|---|---|
|Month|Revenue|
|Jan|$800|
|Feb|$600|
|Mar|$900|

**Goal:**

Make bars **green (even months)** and **red (odd months)**.

---

### **Solution: Calculated Column + Conditional Formatting**

### **Step 1: Create a "Color Code" Column**

```Plain
Color Code = IF(ISEVEN('Sales'[Product ID]), 0, 1)  // 0=Even, 1=Odd
```

### **Step 2: Apply Conditional Formatting**

1. Select the **Bar Chart** → **Format** → **Data Colors**
2. Set:
    - **"0" (Even Months)** → Green
    - **"1" (Odd Months)** → Red

### **Expected Output**

- **Jan (Odd)**: Red bar
- **Feb (Even)**: Green bar
- **Mar (Odd)**: Red bar

---

## **Key Takeaways**

✅ **Top N Analysis:** Use `TOPN + CALCULATE` for dynamic ranking.

✅ **Alternating Colors:** `ISEVEN()` + Conditional Formatting = Quick visual distinction.

✅ **Profit Share:** `(Revenue - Cost)/Total Cost` = Clear profitability metric.