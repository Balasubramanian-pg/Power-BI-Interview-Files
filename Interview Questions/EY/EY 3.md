# **Creating a Category Column with DAX**  

---

## **Problem Statement**  

**Given**:  
A table with a "Product name" column containing various product names (e.g., "Mountain Bike", "Helmet", "Road Bike").  

**Task**:  
Create a calculated "Category" column that:  
- Assigns **"bike"** if the product name contains the word "bike".  
- Assigns **"accessories"** otherwise.  

> [!NOTE]  
> This problem tests your ability to use DAX for text manipulation and conditional logic.  

---

## **Solution Using DAX**  

### **1. Best Approach: `SWITCH` + `CONTAINSSTRING`**  

```dax  
Category =  
SWITCH(  
    TRUE(),  
    CONTAINSSTRING('Products'[Product Name], "bike"), "bike",  
    "accessories"  
)  
```  

**How It Works**:  
- `CONTAINSSTRING()` checks if "bike" exists in the product name (case-insensitive).  
- `SWITCH(TRUE(), ...)` acts like an IF-ELSE chain.  
- Returns "bike" if found, otherwise "accessories".  

> [!TIP]  
> `SWITCH` is more readable than nested `IF` statements for multiple conditions.  

---

### **2. Alternative Solutions**  

**Option A: Using `IF`**  
```dax  
Category =  
IF(  
    CONTAINSSTRING('Products'[Product Name], "bike"),  
    "bike",  
    "accessories"  
)  
```  

**Option B: Case-Sensitive Check**  
```dax  
Category =  
SWITCH(  
    TRUE(),  
    FIND("bike", 'Products'[Product Name], 1, 0) > 0, "bike",  
    "accessories"  
)  
```  

> [!IMPORTANT]  
> Use `FIND` for case-sensitive matching instead of `SEARCH`.  

---

### **Key Concepts Demonstrated**  

✅ **Text Functions**: `CONTAINSSTRING`/`SEARCH`/`FIND` for pattern matching.  
✅ **Conditional Logic**: `SWITCH` vs `IF` statements.  
✅ **Calculated Columns**: Creating new columns with DAX.  

---

### **Example Output**  

| Product Name       | Category    |  
|--------------------|-------------|  
| Mountain Bike      | bike        |  
| Cycling Helmet     | accessories |  
| Road Bike          | bike        |  
| Tire Pump          | accessories |  

---

### **Why This Matters in Interviews**  

- Tests knowledge of **DAX text functions**.  
- Demonstrates **conditional logic** implementation.  
- Shows understanding of **calculated columns** vs. measures.  

> [!TIP]  
> Highlight the choice of `SWITCH` over `IF` for better readability and maintainability.  

**Pro Tip**: For production datasets, consider:  
1. Adding error handling (e.g., for `BLANK` values).  
2. Creating a separate dimension table for categories.  
3. Using Power Query for complex text transformations.  

---

This document provides a clear, step-by-step explanation of creating a category column in Power BI using DAX, addressing common interview questions and challenges.  

> [!TIP]  
> Practice this pattern with different datasets to reinforce your understanding of text manipulation and conditional logic in DAX.  

---
