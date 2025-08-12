# **Highlighting Max and Min Values in Power BI Line Charts**  

## **Scenario Explained**  

The video demonstrates how to **highlight the highest and lowest values in a Power BI line chart**—a common interview question testing DAX skills.  

> [!NOTE]  
> This problem assesses your ability to manipulate context and use DAX functions for conditional calculations.  

### **Problem Statement**  

- **Data**: A line chart shows **sales by country** (e.g., France, Germany, India).  
- **Goal**: Create measures to **identify and display only the max and min sales values** on the chart, making them visually stand out.  

**Example Data**:  
| Country       | Sales |  
|---------------|-------|  
| Germany       | 1600  |  
| France        | 1200  |  
| South Africa  | 150   |  

**Expected Output**:  
- **Max point**: Germany (1600)  
- **Min point**: South Africa (150)  

### **Solution: DAX Measures**  

#### **1. Measure for Maximum Value**  
```dax  
Max Value =  
VAR MaxDataPoint = MAXX(ALL('Table'), [Total Sales])  // Finds global max sales  
VAR CheckMax =  
    IF(  
        [Total Sales] = MaxDataPoint,  // Compares current row's sales to max  
        MaxDataPoint,                  // Returns max if match  
        BLANK()                        // Else returns blank  
    )  
RETURN CheckMax  
```  

#### **2. Measure for Minimum Value**  
```dax  
Min Value =  
VAR MinDataPoint = MINX(ALL('Table'), [Total Sales])  // Finds global min sales  
VAR CheckMin =  
    IF(  
        [Total Sales] = MinDataPoint,  // Compares current row's sales to min  
        MinDataPoint,                  // Returns min if match  
        BLANK()                        // Else returns blank  
    )  
RETURN CheckMin  
```  

### **Key Steps Explained**  

1. **`MAXX`/`MINX` with `ALL`**:  
   - `ALL('Table')` ignores filters to calculate the **global max/min** across all countries.  
   - `MAXX`/`MINX` iterates through the table to find the extreme values.  

2. **`IF` Logic**:  
   - Compares each row’s sales (`[Total Sales]`) to the global max/min.  
   - Returns the value **only if it matches** the extreme; otherwise, returns `BLANK()`.  

3. **Visual Impact**:  
   - When added to the line chart, these measures **display only the max/min points**, hiding other values with `BLANK()`.  

> [!TIP]  
> Use `MAXX`/`MINX` for row-by-row comparisons and `ALL` to ensure global calculations.  

### **Why Not Use `MAX`/`MIN` Directly?**  

- `MAX([Total Sales])` would **not respect row context**—it returns the same value for all rows.  
- `MAXX`/`MINX` iterate row-by-row, allowing dynamic comparison.  

> [!IMPORTANT]  
> `MAX`/`MIN` are scalar functions, while `MAXX`/`MINX` are iterator functions that work with row context.  

### **Pro Tip: Use Conditional Formatting**  

For better visualization:  
1. Add the measures to the line chart’s **tooltip** or **legend**.  
2. Use **conditional formatting** to color max/min points differently (e.g., green for max, red for min).
   
### **Interview Takeaways**  

1. **Understand context transition**: `MAXX`/`MINX` work with row context; `MAX`/`MIN` don’t.  
2. **Use `ALL` to ignore filters**: For global calculations.  
3. **Leverage `BLANK()`**: To hide non-extreme values in visuals.  

> [!TIP]  
> This pattern is reusable for scenarios like highlighting top/bottom performers or flagging outliers in scatter plots.
> 
This document provides a clear, step-by-step explanation of highlighting max and min values in Power BI line charts, addressing common interview questions and challenges.  

> [!TIP]  
> Practice this pattern with different datasets to reinforce your understanding of context manipulation and DAX functions.  
