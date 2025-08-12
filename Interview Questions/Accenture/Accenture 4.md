  

---

## **Scenario-Based DAX Interview Question Using** `**TREATAS()**`

### **Context**

You are given two unconnected tables:

- **Sales Table** (`Sales_Sample7`) with columns `Year` and `SalesValue`
- **Calendar Table** with column `Year`

The goal is to compute **total sales** for only those years present in the **Calendar table**, without establishing an explicit relationship between the two tables.

---

### **Problem Statement**

How can you compute aggregated sales (`SalesValue`) for the subset of years available in the Calendar table, despite no direct relationship between the tables?

---

### **DAX Solution Using** `**TREATAS()**`

```Plain
Sales Amount Required =
CALCULATE(
    SUM(Sales_Sample7[SalesValue]),
    TREATAS(
        VALUES(Calendar[Year]),
        Sales_Sample7[Year]
    )
)
```

---

### **Explanation**

- `CALCULATE` changes the context of evaluation.
- `SUM(Sales_Sample7[SalesValue])` computes total sales.
- `VALUES(Calendar[Year])` returns a **table** of distinct years from the Calendar table.
- `TREATAS()` maps these year values to `Sales_Sample7[Year]`, applying them as a filter **without an explicit relationship**.
- The result is filtered total sales for only those years present in the Calendar table.

---

### **Output Verification**

If the calendar table has years: **2020, 2021, 2022**

And the Sales table contains:

- 2020 → 890
- 2021 → 275
- 2022 → 400

Then the output will be:

`890 + 275 + 400 = 1565`

Years 2018 and 2019 are ignored as they are not in the Calendar table — validating the filter worked.

---

### **Interview Tip**

When posed with disconnected tables and a filtering requirement:

- **Mention** `**TREATAS()**` **confidently.**
- Emphasize **no relationship is needed.**
- Explain how **row context is translated into filter context** across tables.

---

Would you like a visual diagram or a Power BI PBIX file mock-up to accompany this explanation for training or internal documentation purposes?