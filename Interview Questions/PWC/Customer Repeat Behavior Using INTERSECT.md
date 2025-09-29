# **Customer Repeat Behavior Using `INTERSECT`**  
## **Problem Statement**  

**Scenario**:  
You are given:  
- A **Sales Fact Table** containing:  
  - `Order Date`  
  - `Customer Name`  
  - `Total Sale`  
  - `City`  
- A **Calendar Table** linked via `Order Date`.  

**Task**:  
Create measures to calculate:  
1. **Total Customers** who placed orders in the selected date range.  
2. **Last Year’s Customers** who placed orders in the _same_ range shifted back by 365 days.  
3. **Common Customers** between the selected range and the same range in the prior year (i.e., repeat customers).  

> [!NOTE]  
> This problem tests your ability to use DAX for time-based calculations, set operations, and dynamic filtering.  

## **Solution Architecture**  

All measures use:  
- `CALCULATETABLE`  
- `VALUES`  
- `DATESBETWEEN`  
- `INTERSECT`  

> [!TIP]  
> The solution relies on **row context elimination** and **virtual table construction**.  

## **Step-by-Step DAX Solutions**  

### **1. Total Customers in Selected Date Range**  
```dax  
Total Customers =  
COUNTROWS(  
    CALCULATETABLE(  
        VALUES(Sales[Customer Name]),  
        DATESBETWEEN(  
            'Calendar'[Date],  
            MIN('Calendar'[Date]),  
            MAX('Calendar'[Date])  
        )  
    )  
)  
```  

**Explanation**:  
- `VALUES(Sales[Customer Name])` generates a unique list of customers.  
- `CALCULATETABLE` filters this list based on the selected date range.  
- `COUNTROWS` gives the number of unique customers in the range.  

### **2. Last Year’s Customers in the Same Relative Range**  
```dax  
Last Year Customers =  
COUNTROWS(  
    CALCULATETABLE(  
        VALUES(Sales[Customer Name]),  
        SAMEPERIODLASTYEAR(  
            DATESBETWEEN(  
                'Calendar'[Date],  
                MIN('Calendar'[Date]),  
                MAX('Calendar'[Date])  
            )  
        )  
    )  
)  
```  

**Explanation**:  
- Uses `SAMEPERIODLASTYEAR` to shift the selected date range back by one year.  
- Counts unique customers in that shifted range.  

> [!IMPORTANT]  
> `SAMEPERIODLASTYEAR` is more robust than manual `-365` offsets, as it handles leap years and fiscal calendars.  

### **3. Common Customers (Repeat Customers)**  
```dax  
Common Customers =  
COUNTROWS(  
    INTERSECT(  
        CALCULATETABLE(  
            VALUES(Sales[Customer Name]),  
            DATESBETWEEN(  
                'Calendar'[Date],  
                MIN('Calendar'[Date]),  
                MAX('Calendar'[Date])  
            )  
        ),  
        CALCULATETABLE(  
            VALUES(Sales[Customer Name]),  
            SAMEPERIODLASTYEAR(  
                DATESBETWEEN(  
                    'Calendar'[Date],  
                    MIN('Calendar'[Date]),  
                    MAX('Calendar'[Date])  
                )  
            )  
        )  
    )  
)  
```  

**Explanation**:  
- `INTERSECT` performs a set intersection between:  
  - Customers in the selected range.  
  - Customers in the same range last year.  
- `COUNTROWS` returns the number of common customers (repeat customers).  

> [!TIP]  
> `INTERSECT` is powerful for comparing sets of data across different periods.  

## **Expected Output**  

| City        | Total Customers | Last Year Customers | Common Customers |  
|-------------|-------------|--------------------|------------------|  
| Ghaziabad   | 1            | 1                  | 1                |  
| Varanasi    | 2            | 1                  | 1                |  
| Gorakhpur   | 1            | 0                  | 0                |  

> [!NOTE]  
> Measures dynamically respond to the date slicer, providing insights into customer retention.  

## **Why This is a Strong Interview Use Case**  

- Tests understanding of **virtual tables**, **temporal filtering**, and **set operations**.  
- Evaluates ability to **compare time-bound segments** (period-over-period logic).  
- `INTERSECT` demonstrates advanced DAX skills.  

## **Optional Enhancements**  

1. **Dynamic Period Comparison**:  
   Replace hardcoded `-365` with `SAMEPERIODLASTYEAR()` for fiscal calendar compliance.  

2. **Visual Enhancements**:  
   Add a matrix or bar chart by `City` showing:  
   - `Total Customers`  
   - `Last Year Customers`  
   - `Common Customers`  

3. **Percentage Calculation**:  
   ```dax  
   % Repeat Customers =  
   DIVIDE([Common Customers], [Last Year Customers])  
   ```  

> [!TIP]  
> Visualizing retention rates as percentages provides clearer business insights.  

This document provides a comprehensive solution to a common Power BI interview question, showcasing advanced DAX techniques and best practices.  

> [!TIP]  
> Practice this pattern with different datasets to reinforce your understanding of time intelligence and set operations in DAX.  
