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
