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

## How do we find last employee salary as baseline 
(because interviewers just love making you feel like you do not know anything)

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

Actually, I need to verify this - the INDEX function in DAX might not support negative indexing the way I described. Let me reconsider:

For getting the **last row**, you might need a different approach:

```dax
Result =  
VAR LastSalary =  
    CALCULATE(  
        [Total Salary],  
        LASTNONBLANK(
            ALLSELECTED(Employees[Name], Employees[ID]),
            [Total Salary]
        )
    )  
RETURN  
    LastSalary - [Total Salary]
```

Or potentially counting the rows and using that count:

```dax
Result =  
VAR RowCount = COUNTROWS(ALLSELECTED(Employees[Name], Employees[ID]))
VAR LastSalary =  
    CALCULATE(  
        [Total Salary],  
        INDEX(RowCount, ALLSELECTED(Employees[Name], Employees[ID]))  
    )  
RETURN  
    LastSalary - [Total Salary]
```

## LASTNONBLANK Function

**Syntax:**
```dax
LASTNONBLANK(<column>, <expression>)
```

## What it does:
Returns the **last value** in a column where the expression is not blank, based on the column's sort order.

## How it works:

1. **Scans the column** in its natural/sorted order
2. **Evaluates the expression** for each row
3. **Returns the last row** where the expression has a non-blank value
4. It returns the **entire row context**, not just the value

## Example:

```dax
LastSaleDate = 
CALCULATE(
    [Total Sales],
    LASTNONBLANK(Sales[Date], [Total Sales])
)
```

This finds the **last date** that has sales (non-blank sales value) and calculates total sales for that date.

## Key Points:

- **Requires sort order** - The "last" depends on how the column is sorted (dates go chronologically, text goes alphabetically, etc.)
- **Returns a filter context** - It narrows down to that specific row, which you then use in CALCULATE
- **Checks for non-blank** - Only considers rows where the expression evaluates to a non-blank value

## Common Use Cases:
- Last transaction date
- Most recent status
- Latest non-zero balance
- Final recorded value in a time series

Think of it as "give me the filter for the last row that has actual data."
