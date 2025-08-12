# **Extracting Values and Dynamic Titles**  

## **Scenario 1: Extracting First Non-Blank Value Across Columns**  

### **Problem Statement**  
- **Data**: A table with columns:  
  - `Firewall Name`  
  - `First Count`, `Last Count`, `Hit Count` (some values are blank).  
- **Goal**: Create a **calculated column** that returns the **first non-blank value** from the three count columns for each row.  

> [!NOTE]  
> This scenario tests your ability to handle missing data and use DAX functions for conditional logic.  

### **Example Data**  

| Firewall Name | First Count | Last Count | Hit Count | Expected Output |  
|---------------|-------------|------------|----------|-----------------|  
| Firewall 1    | 1           | BLANK      | BLANK    | **1**           |  
| Firewall 2    | BLANK       | BLANK      | 1        | **1**           |  
| Firewall 3    | 1           | 1          | 1        | **1**           |  
| Firewall 4    | BLANK       | 4          | BLANK    | **4**           |  
| Firewall 5    | BLANK       | BLANK      | 5        | **5**           |  

### **Solution: Use `COALESCE`**  

```dax  
Total Count =  
COALESCE(  
    'Table'[First Count],  
    'Table'[Last Count],  
    'Table'[Hit Count]  
)  
```  

**How It Works**:  
- `COALESCE` returns the **first non-blank value** in the order of columns specified.  
- Equivalent to `ISBLANK()` checks with nested `IF` statements, but cleaner.  

> [!TIP]  
> `COALESCE` is ideal for extracting the first available value from multiple columns.  

**Alternative (Nested `IF`)**:  
```dax  
Total Count =  
IF(  
    NOT(ISBLANK([First Count])),  
    [First Count],  
    IF(  
        NOT(ISBLANK([Last Count])),  
        [Last Count],  
        [Hit Count]  
    )  
)  
```  

## **Scenario 2: Dynamic Chart Title Based on Slicer Selection**  

### **Problem Statement**  
- A **slicer** allows users to filter sales data by `Year` (e.g., 2020, 2021, 2022).  
- The **chart title** should update dynamically to show:  
  - _"Total Sales in [Selected Year]"_ when a year is selected.  
  - _"Total Sales in All Years"_ when no year is selected.  

> [!NOTE]  
> This scenario tests your ability to create dynamic, user-friendly reports using DAX.  

### **Solution: Create a Dynamic Title Measure**  

```dax  
Dynamic Title =  
"Total Sales in " &  
IF(  
    HASONEVALUE('Date'[Year]),  
    SELECTEDVALUE('Date'[Year]),  
    "All Years"  
)  
```  

**Steps to Implement**:  
1. **Create the measure** in Power BI.  
2. **Apply to the chart title**:  
   - Go to the chart’s **Format pane** → **Title** → **Text** → Click the **fx (fx)** button.  
   - Select **"Dynamic Title"** measure.  

**How It Works**:  
- `HASONEVALUE()` checks if exactly one year is selected.  
- `SELECTEDVALUE()` returns the selected year (or `"All Years"` if none/multiple are selected).  

> [!IMPORTANT]  
> This approach ensures the title updates automatically based on user interaction.  

---

### **Key Takeaways for Interviews**  

1. **`COALESCE` vs. Nested `IF`**:  
   - Use `COALESCE` for cleaner code when fetching the first non-blank value from multiple columns.  
   - Nested `IF` is more flexible for complex conditions.  

2. **Dynamic Titles**:  
   - Combine `SELECTEDVALUE()` with `HASONEVALUE()` for context-aware titles.  
   - Works with **any slicer** (e.g., regions, products).  

3. **Common Use Cases**:  
   - `COALESCE`: Data cleaning, priority-based value selection.  
   - Dynamic Titles: Dashboards with user-driven filters.  

> [!TIP]  
> For dynamic titles with **multiple selections**, use:  
```dax  
Dynamic Title =  
"Total Sales in " &  
IF(  
    ISFILTERED('Date'[Year]),  
    CONCATENATEX(VALUES('Date'[Year]), [Year], ", "),  
    "All Years"  
)  
```  

This document provides clear, step-by-step solutions to common Power BI DAX challenges, making it ideal for interview preparation and real-world problem-solving.  

> [!TIP]  
> Practice these patterns with different datasets to reinforce your understanding of DAX functions and dynamic reporting.  
