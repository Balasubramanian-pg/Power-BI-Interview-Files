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


### **Key Advanced Concepts to Highlight**
- **Evaluation Context**: Explain how `CALCULATE` alters filter context.
- **Context Transition**: Why row context becomes filter context in measures.
- **Filter Modifiers**: `ALL`, `KEEPFILTERS`, `REMOVEFILTERS`.
- **Performance**: Avoid nested iterators, use variables, leverage aggregations.
- **Advanced Functions**: `GENERATE`, `SUMMARIZE`, `TOPN`, `RANKX`.
