# **DATEADD vs. PARALLELPERIOD and VALUES vs. DISTINCT**  

## **Scenario Explained**  

This Power BI interview question focuses on **time intelligence functions** (`DATEADD` vs. `PARALLELPERIOD`) and the difference between `VALUES` and `DISTINCT` functions.  

> [!NOTE]  
> This problem tests your understanding of time-based calculations and handling unique values in DAX.  

## **Part 1: `DATEADD` vs. `PARALLELPERIOD`**  

### **Problem Statement**  
- **Data**: A table with `Year`, `Month`, and `Total Sales` (e.g., 2020-Jan: 100, 2020-Feb: 150, 2021-Jan: 200, etc.).  
- **Goal**: Calculate **last year’s sales** for each month (e.g., Jan 2021 should show Jan 2020 sales).  

### **Solution**  

### **1. Using `DATEADD` (Returns exact month comparison)**  
```dax  
Last Year Sales (DATEADD) =  
CALCULATE(  
    [Total Sales],  
    DATEADD('Calendar'[Date], -1, YEAR)  // Shifts dates back by 1 year  
)  
```  

**Output**:  
| Year | Month | Total Sales | Last Year Sales (DATEADD) |  
|------|------|-------------|---------------------------|  
| 2021 | Jan  | 200         | **100** (Jan 2020)        |  
| 2021 | Feb  | 300         | **150** (Feb 2020)        |  

> [!TIP]  
> `DATEADD` is ideal for comparing the same period in the previous year (e.g., month-to-month).  

### **2. Using `PARALLELPERIOD` (Returns aggregated period)**  
```dax  
Last Year Sales (PARALLELPERIOD) =  
CALCULATE(  
    [Total Sales],  
    PARALLELPERIOD('Calendar'[Date], -1, YEAR)  // Aggregates entire previous year  
)  
```  

**Output**:  
| Year | Month | Total Sales | Last Year Sales (PARALLELPERIOD) |  
|------|------|-------------|-------------------------------|  
| 2021 | Jan  | 200         | **250** (Sum of Jan+Feb 2020)  |  
| 2021 | Feb  | 300         | **250** (Sum of Jan+Feb 2020)  |  

> [!WARNING]  
> `PARALLELPERIOD` sums all values in the previous year, not month-to-month.  

### **Key Difference**  
- **`DATEADD`**: Compares the **same month** in the previous year.  
- **`PARALLELPERIOD`**: Aggregates the **entire previous year**.  

> [!IMPORTANT]  
> Use `DATEADD` for monthly comparisons and `PARALLELPERIOD` for YTD or annual trends.

## **Part 2: `VALUES` vs. `DISTINCT`**  

### **Problem Statement**  
- Both functions return **unique values** from a column, but they handle **blanks** differently.  

### **Solution**  

### **1. `VALUES` (Includes `BLANK` as a distinct value)**  
```dax  
Unique Customers (VALUES) = COUNTROWS(VALUES('Sales'[Customer]))  
```  

- If `Customer` column has: `["Alice", "Bob", BLANK]` → Returns **3**.  

> [!NOTE]  
> `VALUES` treats `BLANK` as a distinct value.  

### **2. `DISTINCT` (Ignores `BLANK`)**  
```dax  
Unique Customers (DISTINCT) = COUNTROWS(DISTINCT('Sales'[Customer]))  
```  

- Same data → Returns **2** (ignores `BLANK`).  

> [!TIP]  
> Use `DISTINCT` when you want to exclude `BLANK` values from the count.  


### **When to Use Which**  
- **`VALUES`**: When blanks are meaningful (e.g., "Unknown" customers).  
- **`DISTINCT`**: When blanks should be excluded.  

### **Interview Takeaways**  

1. **`DATEADD` vs. `PARALLELPERIOD`**:  
   - `DATEADD` = Exact period shift (e.g., same month last year).  
   - `PARALLELPERIOD` = Aggregates entire shifted period (e.g., full previous year).  

2. **`VALUES` vs. `DISTINCT`**:  
   - `VALUES` includes blanks; `DISTINCT` excludes them.  

3. **Common Use Cases**:  
   - `DATEADD`: Monthly/quarterly comparisons.  
   - `PARALLELPERIOD`: YTD or annual trends.  

> [!IMPORTANT]  
> Understanding these functions is crucial for time-based analysis and handling unique values in Power BI.  

This document provides a clear, step-by-step explanation of `DATEADD`, `PARALLELPERIOD`, `VALUES`, and `DISTINCT`, making it ideal for interview preparation and real-world problem-solving.  

> [!TIP]  
> Practice these concepts with sample datasets to reinforce your understanding of time intelligence and unique value handling in DAX.  
