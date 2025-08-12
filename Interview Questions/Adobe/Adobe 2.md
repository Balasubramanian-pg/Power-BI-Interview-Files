### **Power BI Interview Challenge: Calculating Salary Differences**

### **Problem Statement**

Given a table with employee data:

- **Employee ID**, **Name**, and **Salary** (100, 10, 15, 30, 16)
- Create a measure named "Result" that calculates the difference between each employee's salary and the first employee's salary (100)
- Expected output: 0, 90, 85, 70, 84

### **Solution Using INDEX Function**

1. **Key Insight**:
    - Need to reference the first salary (100) for all rows
    - Subtract each employee's salary from this baseline
2. **DAX Measure**:

```Plain
Result =
VAR FirstSalary =
    CALCULATE(
        [Total Salary],
        INDEX(1, ALLSELECTED(Employees[Name], Employees[ID]))
RETURN
    FirstSalary - [Total Salary]
```

### **How It Works**

1. `INDEX(1,...)` retrieves the first row's salary (absolute position)
2. `ALLSELECTED` ignores row context while maintaining external filters
3. Subtracts current row's salary from the baseline (100)

### **Alternative Approach**

Using `FIRSTNONBLANK`:

```Plain
Result =
VAR FirstSalary =
    CALCULATE(
        [Total Salary],
        FIRSTNONBLANK(Employees[ID], [Total Salary]))
RETURN
    FirstSalary - [Total Salary]
```

### **Key Learnings**

✅ Demonstrates advanced DAX functions (`INDEX`, `ALLSELECTED`)

✅ Solves relative value comparison problems

✅ Shows understanding of context transition

This solution efficiently handles the requirement while demonstrating deep DAX knowledge - exactly what interviewers look for in senior Power BI roles.