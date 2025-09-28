### **Scenario 2: Maximum Sales per Customer (Ignoring Category)**
**Input Table:**
| Category    | Customer | SumOfSales |
|-------------|----------|------------|
| Furniture   | Alice    | 1500       |
| Office Setup| Alice    | 1000       |
| Furniture   | David    | 800        |

**Expected Output:**
| Category    | Customer | SumOfSales | MaxSalesPerCustomer |
|-------------|----------|------------|----------------------|
| Furniture   | Alice    | 1500       | **1500**             |
| Office Setup| Alice    | 1000       | **1500**             |
| Furniture   | David    | 800        | **800**              |

#### **Solution (DAX Measure)**
```dax
MaxSalesPerCustomer =
CALCULATE(
    MAX('Sheet2'[SumOfSales]),
    ALLEXCEPT('Sheet2', 'Sheet2'[Customer])  // Ignore all filters except Customer
)
```

#### **Explanation**
> **⚠️ Key Notes:**
> - `CALCULATE` modifies filter context.
> - `ALLEXCEPT` removes filters on all columns **except** `Customer`.
> - `MAX` computes the highest sale per customer.

---

### **Bonus: SQL Equivalent for Scenario 1**
```sql
SELECT
    EMP,
    STRING_AGG(Department, '|') AS Department,  -- Concatenate with delimiter
    SUM(Salary) AS Salary
FROM
    InputTable
GROUP BY
    EMP;
```

> **💡 Pro Tip:**
> - For **Scenario 2 in SQL**, use a window function:
> ```sql
> SELECT
>     *,
>     MAX(SumOfSales) OVER (PARTITION BY Customer) AS MaxSalesPerCustomer
> FROM
>     InputTable;
> ```
