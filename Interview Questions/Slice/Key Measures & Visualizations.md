# Key Measures & Visualizations  

> [!IMPORTANT]  
> **Before You Begin**: Ensure your data model has proper relationships between tables for accurate measure calculations.  

## 1. Top 5 Products' Profit Share Calculation  

### 1.1 Data Structure Example  

| Product ID | Total Cost | Total Revenue |  
|------------|------------|---------------|  
| P1         | $500       | $800          |  
| P2         | $300       | $600          |  
| ...        | ...        | ...           |  
| P10        | $200       | $400          |  

> [!NOTE]  
> If your table lacks a unique Product ID, create one using `CONCATENATE()` or add an index column.  

### 1.2 DAX Measure Implementation  

```dax  
Top 5 Profit Share =  
VAR Top5Products =  
    TOPN(  
        5,  
        SUMMARIZE('Sales', 'Sales'[Product ID], "Revenue", SUM('Sales'[Total Revenue])),  
        [Revenue], DESC  
    )  
VAR TotalCost_Top5 = CALCULATE(SUM('Sales'[Total Cost]), Top5Products)  
VAR TotalRevenue_Top5 = CALCULATE(SUM('Sales'[Total Revenue]), Top5Products)  
VAR TotalCost_Overall = SUM('Sales'[Total Cost])  
RETURN  
    DIVIDE(TotalRevenue_Top5 - TotalCost_Top5, TotalCost_Overall, 0)  
```  

> [!TIP]  
> Use `ALLSELECTED()` instead of `SUMMARIZE` if you want the calculation to respect slicer selections.  

### 1.3 Implementation Steps  

1. **TOPN Function**: Filters top 5 products by revenue.  
2. **SUMMARIZE**: Creates a temporary table with Product ID and Revenue.  
3. **CALCULATE**: Aggregates cost and revenue for the top 5.  
4. **DIVIDE**: Computes profit share percentage.  

> [!WARNING]  
> Avoid hardcoding values (e.g., `TOPN(5,...)`). Use a parameter table for dynamic "Top N" selection.  

## 2. Alternating Bar Colors in Charts  

### 2.1 Sample Data  

| Month | Revenue |  
|-------|---------|  
| Jan   | $800    |  
| Feb   | $600    |  
| Mar   | $900    |  

> [!NOTE]  
> This technique works for any categorical axis (products, regions, etc.).  

### 2.2 Implementation  

#### 2.2.1 Create Color Code Column  

```dax  
Color Code = IF(ISEVEN(MONTH('Sales'[Date])), 0, 1)  // 0=Even, 1=Odd  
```  

> [!TIP]  
> For non-date fields, use `MOD(RANKX(ALL('Table'[Category]), [Value]), 2)` to alternate colors.  

#### 2.2.2 Apply Conditional Formatting  

1. Select the **Bar Chart** → **Format** → **Data Colors**.  
2. Configure:  
   - **"0" (Even Months)**: Green  
   - **"1" (Odd Months)**: Red  

> [!CAUTION]  
> Power BI may reset conditional formatting when modifying visuals. Save your theme as a JSON file for quick recovery.  

## 3. Key Takeaways  

### 3.1 Top N Analysis  

- Use `TOPN` with `CALCULATE` for dynamic rankings.  
- Always validate results with a separate table visual showing raw rankings.  

> [!IMPORTANT]  
> Profit share calculations should use consistent denominator logic across all reports.  

### 3.2 Visual Enhancements  

- Apply `ISEVEN()` + conditional formatting for color alternation.  
- Use the same color logic in tooltips for consistency.  

### 3.3 Profit Metrics  

- Formula: `(Revenue - Cost)/Total Cost` for profit share.  
- Always include a denominator explanation in report tooltips (e.g., "Profit as % of total cost").  

> [!NOTE]  
> For enterprise deployments, document all DAX measures in a central repository with business logic descriptions.  

> [!WARNING]  
> **Final Checklist**:  
> - [ ] Test all measures with edge cases (zero/null values)  
> - [ ] Verify color accessibility (WCAG contrast ratios)  
> - [ ] Document assumptions in report metadata
