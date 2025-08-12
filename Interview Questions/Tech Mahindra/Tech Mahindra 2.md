### **Power BI Interview Question: Creating a Count Column for Product Occurrences**

---

### **Question**

**Given:** A table with a "Product" column containing iPhone models (e.g., "iPhone 11", "iPhone 12").

**Task:** Create a calculated column that shows the total count of each product's occurrences in the entire table.

**Example Input:**

Product

---

iPhone 11

---

iPhone 12

---

iPhone 11

---

iPhone 13

---

**Expected Output:**

|   |   |
|---|---|
|Product|Count|
|iPhone 11|2|
|iPhone 12|1|
|iPhone 11|2|
|iPhone 13|1|

---

### **Solution Using DAX**

### **Method 1:** `**CALCULATE**` **+** `**ALLEXCEPT**` **(Recommended)**

```Plain
Count =
CALCULATE(
    COUNTROWS(SampleTable),  // Count all rows
    ALLEXCEPT(SampleTable, SampleTable[Product])  // Keep only Product filter
)
```

**How It Works:**

1. `ALLEXCEPT` removes all filters **except** the current row's product value.
2. `COUNTROWS` counts all rows matching that product.
3. The calculation is row-independent—each row shows its product's total occurrences.

**Key Function:**

- `ALLEXCEPT`: Preserves the filter context for only the specified column(s).

---

### **Method 2:** `**COUNTROWS**` **+** `**FILTER**` **(Alternative)**

```Plain
Count =
COUNTROWS(
    FILTER(
        SampleTable,
        SampleTable[Product] = EARLIER(SampleTable[Product])  // Compare to current row
    )
)
```

**Use Case:** When you need more control over filtering logic.

---

### **Technical Breakdown**

|   |   |   |
|---|---|---|
|Concept|Purpose|Example|
|**Row Context**|Evaluates per row in calculated columns|`EARLIER()` references current row|
|**Filter Context**|Modified by `CALCULATE` to override defaults|`ALLEXCEPT` adjusts filters|
|**Iteration**|`FILTER` scans the entire table|Slower for large datasets|

---

### **Performance Considerations**

1. `**ALLEXCEPT**` **is optimized** for VertiPaq engine (faster than `FILTER`).
2. For large tables (>1M rows), test with **DAX Studio** to compare methods.
3. Avoid calculated columns if possible—use **measures** for dynamic analysis.

---

### **Interview Insights**

✅ **Why This Tests Your Skills:**

- Understanding of **context transition** (`CALCULATE` behavior)
- Knowledge of **filter manipulation** (`ALLEXCEPT` vs `ALL`)
- Ability to optimize DAX for **performance**

❌ **Common Mistakes:**

- Using `COUNT()` instead of `COUNTROWS()` (counts non-blank values, not rows)
- Forgetting to handle blanks (`IF(ISBLANK(...))`)

---

### **Pro Tip: Dynamic Measure Version**

For interactive reports, use a **measure** instead:

```Plain
Total Product Count =
VAR SelectedProduct = SELECTEDVALUE(SampleTable[Product])
RETURN
    COUNTROWS(FILTER(ALL(SampleTable), SampleTable[Product] = SelectedProduct))
```

**Advantage:** Updates with slicers/filters.

---

### **Final Output**

|   |   |   |
|---|---|---|
|Product|Count (Column)|Count (Measure)|
|iPhone 11|2|2|
|iPhone 12|1|1|
|iPhone 13|3|3|

**Key Takeaway:**

- Use **calculated columns** for static counts.
- Use **measures** for interactive scenarios.