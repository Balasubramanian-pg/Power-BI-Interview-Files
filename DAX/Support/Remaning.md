
### **Dynamic Filtering & Context**
24. **Dynamic Top N with Slicer Input**  
    ```DAX
    Top N Sales = 
    VAR N = SELECTEDVALUE(Slicer[N], 5) // Slicer for user input
    RETURN
    CALCULATE([Total Sales], TOPN(N, ALL(Product[Name]), [Total Sales]))
    ```
    *Interactive user-driven analysis.*

25. **Exclude Current Row in Matrix**  
    ```DAX
    Sales Excluding Current = 
    [Total Sales] - CALCULATE([Total Sales], REMOVEFILTERS(Product[Name]))
    ```
    *Uses `REMOVEFILTERS` for partial context removal.*

26. **Products Sold in All Regions**  
    ```DAX
    Products in All Regions = 
    COUNTROWS(FILTER(VALUES(Product[ID]), 
      CALCULATE([Total Sales], ALL(Region)) > 0
    ))
    ```
    *Leverages `FILTER` with `ALL` for universal logic.*

---

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

---

### **Hierarchies & Drill-Down**
30. **Dynamic Hierarchy Level Detection**  
    ```DAX
    Drilldown Level = 
    SWITCH(TRUE(),
      ISINSCOPE(Product[Subcategory]), "Subcategory",
      ISINSCOPE(Product[Category]), "Category",
      "Total"
    )
    ```
    *Uses `ISINSCOPE` for responsive visuals.*

31. **Parent-Child Hierarchy Rollup**  
    ```DAX
    Manager Rollup = 
    SUMX(
      PATH(Employee[EmployeeID], Employee[ManagerID]),
      [Total Sales]
    )
    ```
    *Handles organizational hierarchies with `PATH`.*

---

### **Optimization & Scalability**
32. **Measure to Avoid Circular Dependencies**  
    ```DAX
    Safe Measure = 
    VAR Result = [Total Sales]
    RETURN IF(ISBLANK(Result), 0, Result)
    ```
    *Prevents errors in complex models.*

33. **Aggregated Table for Performance**  
    ```DAX
    Precalc Sales = 
    SUMMARIZECOLUMNS(
      'Date'[Year],
      "Total Sales", [Total Sales]
    )
    ```
    *Uses `SUMMARIZECOLUMNS` for optimized storage.*

---

### **Advanced Scenarios**
34. **Market Basket Affinity (A → B)**  
    ```DAX
    Product Affinity = 
    CALCULATE([Total Sales], 
      KEEPFILTERS(Sales[ProductID] = "A"),
      Sales[ProductID] = "B"
    )
    ```
    *Identifies cross-sell opportunities with `KEEPFILTERS`.*

35. **Semi-Additive Measure (Last Balance)**  
    ```DAX
    Last Inventory = 
    CALCULATE(SUM(Inventory[Qty]), 
      LASTNONBLANK('Date'[Date], SUM(Inventory[Qty]))
    )
    ```
    *Critical for inventory/finance (uses `LASTNONBLANK`).*

36. **Dynamic Currency Conversion**  
    ```DAX
    Converted Sales = 
    [Total Sales] * SELECTEDVALUE(ExchangeRates[Rate], 1)
    ```
    *Handles multi-currency models.*

---

### **Error Handling & Debugging**
37. **Debugging Measure with COALESCE**  
    ```DAX
    Debug Sales = 
    COALESCE([Total Sales], 0) // Handles nulls gracefully
    ```
    *Shows defensive coding practices.*

38. **Measure Dependency Tree**  
    ```DAX
    Dependency Check = 
    ISERROR([Total Sales]) // Test for broken dependencies
    ```
    *Useful for troubleshooting complex models.*

---

### **Advanced Iterators**
39. **Ranking with Ties Handled**  
    ```DAX
    Sales Rank = 
    RANKX(ALL(Product), [Total Sales], , DESC, Dense)
    ```
    *Uses `Dense` ranking for tie management.*

40. **Cross-Table Filter Propagation**  
    ```DAX
    Virtual Relationship = 
    CALCULATE([Total Sales], TREATAS(VALUES(Table2[ID]), Table1[ID]))
    ```
    *Simulates relationships with `TREATAS`.*

---

### **Key Advanced Concepts to Highlight**
- **Evaluation Context**: Explain how `CALCULATE` alters filter context.
- **Context Transition**: Why row context becomes filter context in measures.
- **Filter Modifiers**: `ALL`, `KEEPFILTERS`, `REMOVEFILTERS`.
- **Performance**: Avoid nested iterators, use variables, leverage aggregations.
- **Advanced Functions**: `GENERATE`, `SUMMARIZE`, `TOPN`, `RANKX`.

This list covers **complex patterns** like dynamic ranking, semi-additive measures, and cross-table calculations—topics that often separate intermediate from advanced users. Pair these with hands-on practice in Power BI, and you’ll confidently tackle even the toughest DAX interview questions! 🚀
