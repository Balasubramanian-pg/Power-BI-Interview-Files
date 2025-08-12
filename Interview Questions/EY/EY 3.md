---
Pattern:
  - Regex
---
### **Power BI Interview Solution: Creating a Category Column with DAX**

---

### **Problem Statement**

**Given:**

A table with a "Product name" column containing various product names (e.g., "Mountain Bike", "Helmet", "Road Bike").

**Task:**

Create a calculated "Category" column that:

- Assigns **"bike"** if the product name contains the word "bike"
- Assigns **"accessories"** otherwise

---

### **Solution Using DAX**

### **1. Best Approach:** `**SWITCH**` **+** `**CONTAINSSTRING**`

```Plain
Category =
SWITCH(
    TRUE(),
    CONTAINSSTRING('Products'[Product name], "bike"), "bike",
    "accessories"
)
```

**How It Works:**

- `CONTAINSSTRING()` checks if "bike" exists in the product name (case-insensitive)
- `SWITCH(TRUE(), ...)` acts like an IF-ELSE chain
- Returns "bike" if found, otherwise "accessories"

---

### **2. Alternative Solutions**

**Option A: Using** `**IF**`

```Plain
Category =
IF(
    CONTAINSSTRING('Products'[Product name], "bike"),
    "bike",
    "accessories"
)
```

**Option B: Case-Sensitive Check**

```Plain
Category =
SWITCH(
    TRUE(),
    SEARCH("bike", 'Products'[Product name], 1, 0) > 0, "bike",
    "accessories"
)
```

_Note:_ `SEARCH` is case-insensitive; use `FIND` for case-sensitive matching.

---

### **Key Concepts Demonstrated**

✅ **Text Functions**: `CONTAINSSTRING`/`SEARCH` for pattern matching

✅ **Conditional Logic**: `SWITCH` vs `IF` statements

✅ **Calculated Columns**: Creating new columns with DAX

---

### **Example Output**

|   |   |
|---|---|
|Product Name|Category|
|Mountain Bike|bike|
|Cycling Helmet|accessories|
|Road Bike|bike|
|Tire Pump|accessories|

---

### **Why This Matters in Interviews**

- Tests knowledge of **DAX text functions**
- Demonstrates **conditional logic** implementation
- Shows understanding of **calculated vs. measure** columns

**Pro Tip:** For production datasets, consider:

1. Adding error handling (e.g., for BLANK values)
2. Creating a separate dimension table for categories
3. Using Power Query for complex text transformations

Would you like me to expand on any part of this solution? 🚀