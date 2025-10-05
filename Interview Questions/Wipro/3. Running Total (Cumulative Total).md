# **Running Total (Cumulative Total)**  

## **Interview Question**  

**"Can you write the DAX code to calculate a running total (cumulative total)? How would you create a measure that shows the cumulative sales up to a particular date?"**  

> [!NOTE]  
> This problem tests your understanding of DAX functions like `CALCULATE`, `FILTER`, and `ALL`, as well as time intelligence concepts.  

## **What is Running Total?**  

**Running Total (Cumulative Total)** is the sum of all values from the beginning up to the current row/date. It accumulates values progressively.  

**Example**:  
```Plain  
Year | Sales Value | Running Total  
-----|-------------|---------------  
2020 | 890         | 890           (890)  
2021 | 275         | 1165          (890 + 275)  
2022 | 1500        | 2665          (890 + 275 + 1500)  
```  

## **Sample Data Setup**  

### **Sales Sample Table**:  
```Plain  
Product | Order Date | Sales Value  
--------|------------|------------  
A       | 2020-03-15 | 500  
B       | 2020-06-20 | 390  
C       | 2021-02-10 | 275  
D       | 2022-01-05 | 800  
E       | 2022-08-12 | 700  
```  

### **Calendar Table**:  
```Plain  
Date       | Year | Month  
-----------|------|------  
2020-01-01 | 2020 | 1  
2020-01-02 | 2020 | 1  
...        | ...  | ...  
2022-12-31 | 2022 | 12  
```  

### **Model Relationship**:  
- **Sales Sample Table** ↔ **Calendar Table**  
- **Relationship**: One-to-Many  
- **Key**: Date columns  

> [!TIP]  
> Ensure the relationship is correctly defined for time intelligence calculations.  

## **Solution Code**  

### **❌ Incorrect Approach (Common Mistake)**:  
```dax  
Running Total =  
CALCULATE(  
    SUM(Sales[Sales Value]),  
    FILTER(  
        Calendar,  
        Calendar[Date] <= MAX(Calendar[Date])  
    )  
)  
```  

**Problem**: This doesn't ignore existing filters, so it returns the same values as a regular sum.  

### **Correct Solution**:  
```dax  
Running Total =  
CALCULATE(  
    SUM(Sales[Sales Value]),  
    FILTER(  
        ALL(Calendar),  
        Calendar[Date] <= MAX(Calendar[Date])  
    )  
)  
```  

> [!IMPORTANT]  
> The `ALL` function is critical to ignore existing filters and ensure cumulative calculation.  

## **Expected Output**  

### **Year-wise Breakdown**:  
```Plain  
Year | Sales Value | Running Total | Calculation  
-----|-------------|---------------|------------------  
2020 | 890         | 890           | 890  
2021 | 275         | 1,165         | 890 + 275  
2022 | 1,500       | 2,665         | 890 + 275 + 1,500  
```  

### **Visual Representation**:  
```Plain  
2020: ████████████████████ 890  
2021: ████████████████████████████████ 1,165  
2022: ████████████████████████████████████████████████████ 2,665  
```  

## **Code Breakdown**  

### **1. `CALCULATE` Function**  
```dax  
CALCULATE(  
    SUM(Sales[Sales Value]),  // Expression to calculate  
    FILTER(...)               // Filter context  
)  
```  

### **2. `SUM` Function**  
```dax  
SUM(Sales[Sales Value])  // Sums the sales values  
```  

### **3. `FILTER` Function**  
```dax  
FILTER(  
    ALL(Calendar),                    // Table to filter  
    Calendar[Date] <= MAX(Calendar[Date])  // Filter condition  
)  
```  

### **4. `ALL` Function (Critical Part)**  
```dax  
ALL(Calendar)  // Ignores existing filters and returns all calendar rows  
```  

### **5. Filter Condition**  
```dax  
Calendar[Date] <= MAX(Calendar[Date])  // Include all dates up to current row's date  
```  

> [!TIP]  
> The `ALL` function ensures the filter context is reset, allowing cumulative calculation.  

## **Why `ALL()` Function is Critical**  

### **Without `ALL()`**:  
- Existing filters remain active.  
- Only the current row's date is considered.  
- Result = Same as regular SUM (no cumulative effect).  

### **With `ALL()`**:  
- Ignores all existing filters on the Calendar table.  
- Considers all rows in the Calendar table.  
- Applies new filter condition (dates <= current date).  
- Result = True cumulative total.  

## **Step-by-Step Logic**  

1. **`ALL(Calendar)`** - Remove all existing filters from the Calendar table.  
2. **`Calendar[Date] <= MAX(Calendar[Date])`** - Keep only dates up to the current row's maximum date.  
3. **`SUM(Sales[Sales Value])`** - Sum sales values for the filtered date range.  
4. **`CALCULATE`** - Apply the filter context and calculate the result.  

## **Alternative Approaches**  

### **Using `SUMX` with `FILTER`**:  
```dax  
Running Total Alt =  
SUMX(  
    FILTER(  
        ALL(Calendar),  
        Calendar[Date] <= MAX(Calendar[Date])  
    ),  
    CALCULATE(SUM(Sales[Sales Value]))  
)  
```  

### **Using Variables for Clarity**:  
```dax  
Running Total Clear =  
VAR CurrentDate = MAX(Calendar[Date])  
VAR FilteredDates =  
    FILTER(  
        ALL(Calendar),  
        Calendar[Date] <= CurrentDate  
    )  
RETURN  
    CALCULATE(  
        SUM(Sales[Sales Value]),  
        FilteredDates  
    )  
```  

> [!TIP]  
> Variables improve readability and performance by avoiding redundant calculations.  

## **Interview Key Points**  

1. **`ALL()` Function is Essential** - Without it, running total won't work.  
2. **`FILTER` Logic** - Must include all dates up to the current date.  
3. **`MAX()` Function** - Gets the maximum date in the current context.  
4. **`CALCULATE` Context** - Modifies filter context for calculation.  
5. **Common Mistake** - Forgetting `ALL()` function leads to incorrect results.  

## **When to Use Running Total**  

- **Financial Reports** - Cumulative revenue, expenses.  
- **Sales Analysis** - Year-to-date sales, quarterly cumulative.  
- **Performance Tracking** - Cumulative targets vs. actual.  
- **Inventory Management** - Cumulative stock levels.  
- **Project Management** - Cumulative costs, hours.  

> [!IMPORTANT]  
> This DAX pattern is fundamental for time intelligence calculations in Power BI and is frequently tested in interviews.  

This document provides a comprehensive solution to calculating running totals in Power BI, addressing common interview questions and challenges.  

> [!TIP]  
> Practice this pattern with different datasets to reinforce your understanding of DAX and time intelligence.  
