# Scenario-Based DAX Interview Question Using `TREATAS()`  

## **Context**  

You are given two **unconnected tables**:  
- **Sales Table** (`Sales_Sample7`) with columns `Year` and `SalesValue`.  
- **Calendar Table** with column `Year`.  

**Goal**: Compute **total sales** for only those years present in the **Calendar table**, without establishing an explicit relationship between the two tables.  

> [!NOTE]  
> This problem tests your ability to manipulate filter contexts in DAX without relying on relationships.  

## **Problem Statement**  
How can you compute aggregated sales (`SalesValue`) for the subset of years available in the Calendar table, despite no direct relationship between the tables?  

> [!IMPORTANT]  
> The challenge is to filter the Sales table based on the Calendar table without a relationship.  

## **DAX Solution Using `TREATAS()`**  

```dax  
Sales Amount Required =  
CALCULATE(  
    SUM(Sales_Sample7[SalesValue]),  
    TREATAS(  
        VALUES(Calendar[Year]),  
        Sales_Sample7[Year]  
    )  
)  
```  
## **Explanation**  

1. **`CALCULATE`**: Modifies the filter context for the calculation.  
2. **`SUM(Sales_Sample7[SalesValue])`**: Computes the total sales.  
3. **`VALUES(Calendar[Year])`**: Returns a **table** of distinct years from the Calendar table.  
4. **`TREATAS()`**: Maps the distinct years from the Calendar table to the `Sales_Sample7[Year]` column, effectively filtering the Sales table without a relationship.  

> [!TIP]  
> `TREATAS()` is the key function here, as it allows you to apply a filter from one table to another without a relationship.  

## **Output Verification**  

**Assumptions**:  
- Calendar table has years: **2020, 2021, 2022**.  
- Sales table contains:  
  - 2020 → 890  
  - 2021 → 275  
  - 2022 → 400  

**Result**:  
`890 + 275 + 400 = 1565`  

**Validation**:  
Years 2018 and 2019 are ignored because they are not present in the Calendar table, confirming the filter worked correctly.  

> [!IMPORTANT]  
> This demonstrates that `TREATAS()` successfully filters the Sales table based on the Calendar table’s years.  

## **Interview Tip**  

When posed with disconnected tables and a filtering requirement:  
- **Mention `TREATAS()` confidently.**  
- **Emphasize that no relationship is needed.**  
- **Explain how row context is translated into filter context** across tables.  

> [!TIP]  
> Highlighting `TREATAS()` shows advanced DAX knowledge and problem-solving skills.  

## **Visual Diagram Suggestion**  

For training or internal documentation, consider a diagram showing:  
1. Two separate tables (Sales and Calendar).  
2. An arrow from the Calendar table’s `Year` column to the Sales table’s `Year` column, labeled with `TREATAS()`.  
3. A filter icon over the Sales table indicating the applied filter.  

> [!NOTE]  
> A visual diagram can help reinforce the concept of `TREATAS()` as a "virtual filter" across tables.  

This document provides a clear, step-by-step explanation of using `TREATAS()` to solve a common DAX filtering problem. It’s designed to help candidates understand and articulate the solution effectively in interviews. 

> [!TIP]  
> Practice this pattern with different datasets to reinforce your understanding of `TREATAS()` and filter context manipulation.  
