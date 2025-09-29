
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
