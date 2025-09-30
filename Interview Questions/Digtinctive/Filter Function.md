**Question:**
How do I create a *Performance* column in Power BI that shows **“High”** if `Sales Amount > 20` and **“Low”** otherwise?

**DAX Solution (Calculated Column):**

```DAX
Performance =
IF ( 'Sales'[Sales Amount] > 20, "High", "Low" )
```

> [!NOTE]
> This is written as a **calculated column**, meaning it evaluates row by row for each record in your `Sales` table.

> [!TIP]
> Use a **calculated column** when you need the classification (“High”/“Low”) to exist at the row level and be filterable like other columns.

> [!IMPORTANT]
> If your goal is to **aggregate performance at a higher level** (e.g., by customer or by region), you’ll need a **measure** instead of a column.

**DAX Solution (Measure):**

```DAX
Performance Measure =
IF ( SUM('Sales'[Sales Amount]) > 20, "High", "Low" )
```

> [!WARNING]
> Measures calculate dynamically based on context (e.g., a chart grouped by customer), so the logic applies to totals, not individual rows.

> [!CAUTION]
> Using the wrong approach (column vs measure) can lead to misleading results — choose based on whether you need row-level classification or aggregated evaluation.

Here’s how the same logic looks if you deliberately use `CALCULATE`:

---

**Measure with `CALCULATE`:**

```DAX
Performance Measure =
IF (
    CALCULATE ( SUM ( 'Sales'[Sales Amount] ) ) > 20,
    "High",
    "Low"
)
```

---

> [!NOTE]
> This works exactly the same as `IF(SUM(...))`, because there’s no extra filter inside `CALCULATE`.

> [!TIP]
> You bring in `CALCULATE` only when you need to **change the filter context**, like checking totals for a subset of products, dates, or customers.

Example:

```DAX
Performance Product A =
IF (
    CALCULATE ( SUM ( 'Sales'[Sales Amount] ), 'Sales'[Product] = "A" ) > 20,
    "High",
    "Low"
)
```

> [!IMPORTANT]
> That’s where `CALCULATE` becomes meaningful: applying conditions **inside** it.
