# **Power BI Interview Guide: Understanding ALL, ALLSELECTED, and ALLEXCEPT Functions**  

## **1. `ALL` Function**  

### **Purpose**  
- **Removes all filters** from a specified column or table.  
- Returns **every row/value** as if no filters were applied.  

> [!IMPORTANT]  
> Use `ALL` when you need to ignore all filters on a specific column or table.  

### **Use Cases**  
Calculating **grand totals** regardless of visual filters.  
Resetting filter context in complex measures.  

### **Example**  
```dax  
Total Sales (All Products) =  
CALCULATE(  
    SUM(Sales[Amount]),  
    ALL(Products[ProductName])  // Ignores any filters on ProductName  
)  
```  

> [!TIP]  
> This measure will show the total sales across all products, even if a visual filters for specific products.  


## **2. `ALLSELECTED` Function**  

### **Purpose**  
- **Retains explicit filters** (e.g., slicers, cross-filtering) while removing other context filters.  
- Useful for **comparisons** (e.g., "Show me sales for selected regions vs. overall").  

> [!NOTE]  
> `ALLSELECTED` is ideal for creating measures that respect user-applied filters but ignore other contextual filters.  

### **Use Cases**  
Dynamic totals in **slicer-driven reports**.  
Creating **relative percentages** (e.g., "Selected Region vs. All Regions").  

### **Example**  
```dax  
% of Selected Regions =  
DIVIDE(  
    [Total Sales],  
    CALCULATE([Total Sales], ALLSELECTED(Regions[Region]))  // Respects slicer selections  
)  
```  

> [!TIP]  
> If a user selects "East" and "West" in a slicer, the denominator reflects only those regions.  

## **3. `ALLEXCEPT` Function**  

### **Purpose**  
- **Removes all filters except those on specified columns**.  
- Focuses calculations on **one dimension** while ignoring others.  

> [!IMPORTANT]  
> Use `ALLEXCEPT` to isolate the impact of a single dimension in your calculations.  

### **Use Cases**  
Calculating **product-level totals** while ignoring date/category filters.  
Simplifying measures that need to ignore certain filters.  

### **Example**  
```dax  
Sales by Color =  
CALCULATE(  
    SUM(Sales[Amount]),  
    ALLEXCEPT(Products, Products[Color])  // Keeps only Color filters  
)  
```  

> [!TIP]  
> This measure shows sales per color, even if other filters (e.g., `Size` or `Brand`) are applied.  


## **Key Differences Summary**  

| **Function**       | **Behavior**                                      | **Example Scenario**                     |  
|--------------------|--------------------------------------------------|------------------------------------------|  
| **`ALL`**          | Removes **all** filters                          | Grand total ignoring all slicers         |  
| **`ALLSELECTED`**  | Keeps **user-applied** filters (slicers)         | "Selected vs. Total" comparisons         |  
| **`ALLEXCEPT`**    | Removes **all except specified columns**         | Product sales by color only              |  


## **Interview Pro Tips**  

🔹 **`ALL` vs. `REMOVEFILTERS`**:  
- `REMOVEFILTERS` (newer) is preferred—it’s clearer and does the same thing.  

> [!WARNING]  
> Avoid using `ALL` when `REMOVEFILTERS` is more appropriate.  

🔹 **Performance**:  
- `ALLEXCEPT` is **faster** than nested `ALL` + `KEEPFILTERS`.  

> [!TIP]  
> Optimize performance by using `ALLEXCEPT` for complex filtering scenarios.  

🔹 **Common Pitfalls**:  
- Misusing `ALLSELECTED` in complex measures can lead to circular logic.  
- Forgetting that `ALLEXCEPT` still respects **row-level security (RLS)**.  

## **Real-World Application**  

**Scenario**: A report shows sales by region, with a slicer for product categories.  

```dax  
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

> [!IMPORTANT]  
> These measures demonstrate how to handle filter contexts effectively in real-world scenarios.  

## **Final Thought**  

Mastering these functions is **critical for advanced DAX** and often separates junior from senior Power BI developers.  

> [!TIP]  
> Practice creating measures that use `ALL`, `ALLSELECTED`, and `ALLEXCEPT` to reinforce your understanding of filter context manipulation.  

This document provides a clear, step-by-step explanation of the `ALL`, `ALLSELECTED`, and `ALLEXCEPT` functions, making it ideal for interview preparation and real-world problem-solving.  

> [!TIP]  
> Use sample datasets to experiment with these functions and observe how they manipulate filter contexts.  
