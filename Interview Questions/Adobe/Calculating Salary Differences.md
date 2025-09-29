# Calculating Salary Differences  

## **Problem Statement**  

Given a table with employee data:  
- **Employee ID**, **Name**, and **Salary** (100, 10, 15, 30, 16).  
- Create a measure named **"Result"** that calculates the difference between each employee's salary and the first employee's salary (100).  
- **Expected output**: 0, 90, 85, 70, 84.  

> [!NOTE]  
> This problem tests your ability to handle relative calculations and context transitions in DAX.  

## **Solution Using `INDEX` Function**  

### **1. Key Insight**  
- Need to reference the first salary (100) for all rows.  
- Subtract each employee's salary from this baseline.  

### **2. DAX Measure**  
```dax  
Result =  
VAR FirstSalary =  
    CALCULATE(  
        [Total Salary],  
        INDEX(1, ALLSELECTED(Employees[Name], Employees[ID]))  
    )  
RETURN  
    FirstSalary - [Total Salary]  
```  

## **How It Works**  

1. **`INDEX(1,...)`**:  
   - Retrieves the salary from the **first row** (absolute position 1).  
   - Ensures the baseline salary (100) is consistently referenced.  

2. **`ALLSELECTED`**:  
   - Ignores row context while maintaining external filters.  
   - Ensures the first salary is fetched independently of the current row.  

3. **Subtraction**:  
   - Subtracts each employee's salary from the baseline (100).  

> [!IMPORTANT]  
> `INDEX` and `ALLSELECTED` are crucial for fetching the first salary without being affected by row context.  

## **Alternative Approach Using `FIRSTNONBLANK`**  

```dax  
Result =  
VAR FirstSalary =  
    CALCULATE(  
        [Total Salary],  
        FIRSTNONBLANK(Employees[ID], [Total Salary])  
    )  
RETURN  
    FirstSalary - [Total Salary]  
```  

> [!TIP]  
> `FIRSTNONBLANK` is a simpler alternative when you need the first non-blank value in a column.  

## **Key Learnings**  

**Demonstrates advanced DAX functions** (`INDEX`, `ALLSELECTED`, `FIRSTNONBLANK`).  
**Solves relative value comparison problems** efficiently.  
**Shows understanding of context transition** in DAX.  

> [!IMPORTANT]  
> This solution highlights deep DAX knowledge, which is essential for senior Power BI roles.  

This document provides a clear, step-by-step explanation of calculating salary differences in Power BI using advanced DAX techniques. It’s designed to help candidates understand and articulate the solution effectively in interviews.  

> [!TIP]  
> Practice this pattern with different datasets to reinforce your understanding of relative calculations and context transitions in DAX.

## How do we find last employee salary as baseline (because interviewers just love making you feel like you do not know anything)

Just change `INDEX(1, ...)` to `INDEX(-1, ...)`:

```dax
Result =  
VAR LastSalary =  
    CALCULATE(  
        [Total Salary],  
        INDEX(-1, ALLSELECTED(Employees[Name], Employees[ID]))  
    )  
RETURN  
    LastSalary - [Total Salary]
```

## How INDEX works:
- **INDEX(1, ...)** - First row
- **INDEX(-1, ...)** - Last row
- **INDEX(2, ...)** - Second row
- **INDEX(-2, ...)** - Second to last row

So `-1` gives you the last employee in the current filter context, and the measure will now show the difference between each employee's salary and the last employee's salary.
