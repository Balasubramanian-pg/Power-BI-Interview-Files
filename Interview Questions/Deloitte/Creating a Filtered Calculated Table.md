# **Creating a Filtered Calculated Table in Power BI Using DAX**  

## Scenario Summary  

**Objective**:  
Create a **calculated table** using DAX that includes only 3 specific columns from a larger table and satisfies **3 filter conditions**.  

> [!NOTE]  
> This problem tests your ability to create calculated tables, apply filters, and select specific columns using DAX.  


## Problem Statement

You’re given a table with multiple columns (Product ID, Product Name, Price, Color, Quantity, etc.).  

**Task**:  
Create a new table with only three columns:  
1. Product ID  
2. Product Name  
3. Product Price  

And apply the following filters:  
- `Product ID <= 100`  
- `Product Name` starts with `"S"` (case-insensitive)  
- `Price >= 500`  

> [!IMPORTANT]  
> The solution must use DAX to create a calculated table with the specified filters and columns.  

---

## **Concepts Tested**  

- Creating calculated tables using DAX  
- Use of `CALCULATETABLE`  
- Filtering with `SEARCH()`  
- Column selection with `SUMMARIZECOLUMNS`  

---

## ** DAX Solution**  

```dax  
Required Table =  
CALCULATETABLE(  
    SUMMARIZECOLUMNS(  
        Products[Product ID],  
        Products[Product Name],  
        Products[Price]  
    ),  
    Products[Product ID] <= 100,  
    Products[Price] >= 500,  
    SEARCH("s", Products[Product Name], 1, BLANK()) = 1  
)  
```  

---

###  Explanation  

#### **1. `CALCULATETABLE`**  
- **Purpose**: Used to create a new table filtered based on specific conditions.  
- **Parameters**: Accepts a base table and filter expressions.  

> [!TIP]  
> `CALCULATETABLE` is ideal for creating filtered tables in DAX.  

#### **2. `SUMMARIZECOLUMNS`**  
- **Purpose**: Extracts only the required three columns from the original table.  
- **Columns**: `Product ID`, `Product Name`, `Price`.  

> [!NOTE]  
> `SUMMARIZECOLUMNS` is used to select and group specific columns.  

#### **3. Filter Conditions**  
- `Products[Product ID] <= 100`: Numeric filter.  
- `Products[Price] >= 500`: Numeric filter.  
- `SEARCH("s", Products[Product Name], 1, BLANK()) = 1`:  
  - Searches for the letter "s" at the **first character** (position 1) of the `Product Name`.  
  - Case-insensitive, unlike `FIND()`.  
  - Returns rows where the name starts with "s" or "S".  

> [!IMPORTANT]  
> Use `SEARCH()` for case-insensitive text filtering.  

---

### **Expected Output**  

A new table with:  

| Product ID | Product Name | Price |  
|------------|--------------|------|  
| 84         | Shoes        | 520  |  
| 99         | Shampoo      | 750  |  
| 100        | Speakers     | 1200 |  

> [!NOTE]  
> Actual values depend on the source data.  

---

### ** Best Practice Notes**  

- **Use `SEARCH()` over `FIND()`** for case-insensitive checks.  
- **`CALCULATETABLE()` + `SUMMARIZECOLUMNS()`** is a clean way to filter and project required columns in one go.  
- Avoid `SELECTCOLUMNS()` here as it doesn’t natively support complex filters like `CALCULATETABLE()` does.  

---

### **Alternate Solution #1: Using `SELECTCOLUMNS` + `FILTER`**  

This version is more readable and modular, especially for row-based filtering.  

**DAX Code**:  
```dax  
Required Table =  
FILTER(  
    SELECTCOLUMNS(  
        Products,  
        "Product ID", Products[Product ID],  
        "Product Name", Products[Product Name],  
        "Price", Products[Price]  
    ),  
    Products[Product ID] <= 100 &&  
    Products[Price] >= 500 &&  
    SEARCH("s", Products[Product Name], 1, BLANK()) = 1  
)  
```  

> [!TIP]  
> Use `SELECTCOLUMNS` + `FILTER` when you need to apply row-level filters and select specific columns.  

---

### ** Alternate Solution #2: With `ADDCOLUMNS` for Extra Logic**  

If you need to compute additional fields or flags while keeping filters clean:  

```dax  
Required Table =  
FILTER(  
    ADDCOLUMNS(  
        Products,  
        "StartsWithS", SEARCH("s", Products[Product Name], 1, BLANK())  
    ),  
    Products[Product ID] <= 100 &&  
    Products[Price] >= 500 &&  
    [StartsWithS] = 1  
)  
```  

Then use `SELECTCOLUMNS` on this result if you want to keep only the 3 columns:  
```dax  
SELECTCOLUMNS(  
    Required Table,  
    "Product ID", Products[Product ID],  
    "Product Name", Products[Product Name],  
    "Price", Products[Price]  
)  
```  

> [!NOTE]  
> `ADDCOLUMNS` is useful for adding computed columns before applying filters.  

---

### ** When to Use Which**  

| **Method**                          | **Use Case**                                      |  
|-------------------------------------|--------------------------------------------------|  
| `CALCULATETABLE` + `SUMMARIZECOLUMNS` | Best for grouped or aggregated results           |  
| `FILTER` + `SELECTCOLUMNS`          | Best for clean row-level filters without grouping|  
| `ADDCOLUMNS` + `FILTER`             | Best when you want to precompute flags or metrics|  

---

This document provides a clear, step-by-step explanation of creating a filtered calculated table in Power BI using DAX, addressing common interview questions and challenges.  

> [!TIP]  
> Practice these techniques with different datasets to reinforce your understanding of calculated tables and filtering in DAX.  
