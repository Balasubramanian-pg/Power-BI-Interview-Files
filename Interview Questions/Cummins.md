### **Power BI Interview Guide: Understanding ALL, ALLSELECTED, and ALLEXCEPT Functions**

---

### **1.** `**ALL**` **Function**

### **Purpose**

- **Removes all filters** from a specified column or table.
- Returns **every row/value** as if no filters were applied.

### **Use Cases**

✅ Calculating **grand totals** regardless of visual filters

✅ Resetting filter context in complex measures

### **Example**

```Plain
Total Sales (All Products) =
CALCULATE(
    SUM(Sales[Amount]),
    ALL(Products[ProductName])  // Ignores any filters on ProductName
)
```

**Output:** Shows total sales even if a visual filters for specific products.

---

### **2.** `**ALLSELECTED**` **Function**

### **Purpose**

- **Retains explicit filters** (e.g., slicers, cross-filtering) while removing other context filters.
- Useful for **comparisons** (e.g., "Show me sales for selected regions vs. overall").

### **Use Cases**

✅ Dynamic totals in **slicer-driven reports**

✅ Creating **relative percentages** (e.g., "Selected Region vs. All Regions")

### **Example**

```Plain
% of Selected Regions =
DIVIDE(
    [Total Sales],
    CALCULATE([Total Sales], ALLSELECTED(Regions[Region])  // Respects slicer selections
)
```

**Output:** If a user selects "East" and "West" in a slicer, the denominator reflects only those regions.

---

### **3.** `**ALLEXCEPT**` **Function**

### **Purpose**

- **Removes all filters except those on specified columns**.
- Focuses calculations on **one dimension** while ignoring others.

### **Use Cases**

✅ Calculating **product-level totals** while ignoring date/category filters

✅ Simplifying measures that need to ignore certain filters

### **Example**

```Plain
Sales by Color =
CALCULATE(
    SUM(Sales[Amount]),
    ALLEXCEPT(Products, Products[Color])  // Keeps only Color filters
)
```

**Output:** Shows sales per color, even if other filters (e.g., `Size` or `Brand`) are applied.

---

### **Key Differences Summary**

|   |   |   |
|---|---|---|
|Function|Behavior|Example Scenario|
|`**ALL**`|Removes **all** filters|Grand total ignoring all slicers|
|`**ALLSELECTED**`|Keeps **user-applied** filters (slicers)|"Selected vs. Total" comparisons|
|`**ALLEXCEPT**`|Removes **all except specified columns**|Product sales by color only|

---

### **Interview Pro Tips**

🔹 `**ALL**` **vs.** `**REMOVEFILTERS**`:

- `REMOVEFILTERS` (newer) is preferred—it’s clearer and does the same thing.

🔹 **Performance**:

- `ALLEXCEPT` is **faster** than nested `ALL` + `KEEPFILTERS`.

🔹 **Common Pitfalls**:

- Misusing `ALLSELECTED` in complex measures can lead to circular logic.
- Forgetting that `ALLEXCEPT` still respects **row-level security (RLS)**.

---

### **Real-World Application**

**Scenario:** A report shows sales by region, with a slicer for product categories.

```Plain
// Dynamic % of Total
% of Total =
DIVIDE(
    [Total Sales],
    CALCULATE([Total Sales], ALLSELECTED(Products[Category]))  // Respects slicer
)

// Color Contribution (ignoring other filters)
Color Contribution =
CALCULATE(
    [Total Sales],
    ALLEXCEPT(Products, Products[Color])
)
```

---

### **Final Thought**

Mastering these functions is **critical for advanced DAX** and often separates junior from senior Power BI developers.