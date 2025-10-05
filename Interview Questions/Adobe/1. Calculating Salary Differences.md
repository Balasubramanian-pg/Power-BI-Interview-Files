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

## How do we find the previous employee salary as baseline 
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
## How do we find last employee's salary as baseline
To use the **last employee as the baseline** (instead of the previous row), you need to modify the approach. Here are several methods:

## Method 1: Fixed Last Employee Baseline

```dax
Result = 
VAR LastEmployeeSalary =
    CALCULATE(
        [Total Salary],
        LASTNONBLANK(Employees[Name], [Total Salary]),
        ALLSELECTED(Employees)
    )
RETURN
    [Total Salary] - LastEmployeeSalary
```

## Method 2: Using MAXX with TOPN

```dax
Result = 
VAR LastEmployeeSalary =
    MAXX(
        TOPN(1, VALUES(Employees[Name]), Employees[Name], DESC),
        [Total Salary]
    )
RETURN
    [Total Salary] - LastEmployeeSalary
```

## Method 3: Using LASTDATE (if Name is sorted)

```dax
Result = 
VAR LastEmployeeSalary =
    CALCULATE(
        [Total Salary],
        LASTDATE(Employees[Name]),
        ALLSELECTED(Employees)
    )
RETURN
    [Total Salary] - LastEmployeeSalary
```

## Method 4: More Robust - Handle Multiple Columns

```dax
Result = 
VAR LastEmployee =
    CALCULATE(
        LASTNONBLANK(Employees[Name], 1),
        ALLSELECTED(Employees)
    )
VAR LastEmployeeSalary =
    CALCULATE(
        [Total Salary],
        Employees[Name] = LastEmployee,
        ALLSELECTED(Employees)
    )
RETURN
    [Total Salary] - LastEmployeeSalary
```

## Example Output:
If your sorted table shows:
```
Name    | Total Salary | Result
Alice   | $60,000      | $10,000 (60,000 - 50,000) 
John    | $50,000      | $0 (50,000 - 50,000)
Mike    | $45,000      | -$5,000 (45,000 - 50,000)
```
*(Assuming "Mike" is the last employee in the sort order)*

## Key Differences from Original:
- **Original**: Compared each row to its immediate predecessor
- **New**: Compares all rows to the **last employee** in the current context
- **Baseline**: Fixed reference point (last employee) instead of moving window

## Pro Tip:
If you want to **reverse this** (use first employee as baseline), replace `LASTNONBLANK` with `FIRSTNONBLANK` or `LASTDATE` with `FIRSTDATE`.

The **Method 1** with `LASTNONBLANK` is generally the most reliable approach.
