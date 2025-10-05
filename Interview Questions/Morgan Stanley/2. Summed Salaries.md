# **Scenario 1: Grouping Employees with Concatenated Departments and Summed Salaries**
**Input Table:**
| EMP | Department | Salary |
|-----|------------|--------|
| AA  | D1         | 300    |
| AA  | D2         | 300    |
| CC  | D3         | 400    |

**Expected Output:**
| EMP | Department | Salary |
|-----|------------|--------|
| AA  | D1|D2      | 600    |
| CC  | D3         | 400    |

#### **Solution (DAX Code)**
```dax
OutputTable =
SUMMARIZE(
    'Sheet1',
    'Sheet1'[EMP],  // Group by EMP
    "DPT", CONCATENATEX(
        VALUES('Sheet1'[Department]),  // Unique departments per EMP
        'Sheet1'[Department],
        "|"  // Delimiter
    ),
    "Salary", SUM('Sheet1'[Salary])  // Sum salaries per EMP
)
```

#### **Explanation**
> **⚠️ Key Notes:**
> - `SUMMARIZE` groups data by `EMP`.
> - `CONCATENATEX` + `VALUES` concatenates unique departments with `|`.
> - `SUM` aggregates salaries for each group.
