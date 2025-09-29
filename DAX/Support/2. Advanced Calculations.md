### **Advanced Calculations**
27. **Weighted Average Unit Price**  
    ```DAX
    Weighted Avg Price = 
    DIVIDE(
      SUMX(Sales, Sales[Quantity] * Sales[UnitPrice]),
      SUM(Sales[Quantity])
    )
    ```
    *Critical for financial metrics; uses `SUMX`.*

28. **Cumulative Percentage of Total**  
    ```DAX
    Cumulative % = 
    VAR Total = CALCULATE([Total Sales], ALL(Product))
    RETURN
    DIVIDE([Running Total], Total)
    ```
    *Combines running totals and percentage calculations.*

29. **Linear Regression Slope (Sales Trend)**  
    ```DAX
    Trend Slope = 
    SLOPE.X(
      FILTER(VALUES('Date'[Month]), [Total Sales] > 0),
      'Date'[Month], [Total Sales]
    )
    ```
    *Statistical analysis within DAX (uses `SLOPE.X`).*
