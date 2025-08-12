---
Pattern:
  - Many to Many Relationship
---
### **Power BI Interview Solution: Resolving Many-to-Many Relationships with Bridge Tables**

---

### **Problem Statement**

**Scenario:**

You have two tables with a common field (e.g., `CustomerName`) where neither table contains unique values, creating a many-to-many relationship that causes ambiguous results in visualizations.

**Example Tables:**

1. **Customers Table**
    
    |   |   |
    |---|---|
    |CustomerName|Region|
    |John Smith|West|
    |John Smith|East|
    |Sarah Lee|North|
    
2. **Transactions Table**
    
    |   |   |
    |---|---|
    |CustomerName|Amount|
    |John Smith|$100|
    |John Smith|$200|
    

---

### **Step-by-Step Solution**

### **1. Create a Bridge Table**

```Plain
// Power Query M Code to create bridge table
let
    Source = Transactions,  // Or use Customers table
    #"Removed Columns" = Table.SelectColumns(Source,{"CustomerName"}),
    #"Removed Duplicates" = Table.Distinct(#"Removed Columns")
in
    #"Removed Duplicates"
```

**Resulting Bridge Table:**

CustomerName

---

John Smith

---

Sarah Lee

---

### **2. Establish Relationships**

```mermaid
graph LR
    A[Customers] -->|One-to-Many| B[Bridge]
    B[Bridge] -->|One-to-Many| C[Transactions]
```

**Relationship Properties:**

- Cross-filter direction: **Single** (Bridge → Both tables)
- Cardinality: **1:N** for both relationships

### **3. DAX Measures for Accurate Reporting**

```Plain
Total Sales =
SUM(Transactions[Amount])

// Filter-safe measure
Filtered Sales =
CALCULATE(
    [Total Sales],
    USERELATIONSHIP(Bridge[CustomerName], Transactions[CustomerName])
)
```

---

### **Key Technical Considerations**

### **Why This Works**

1. **Eliminates Ambiguity**: Bridge table contains unique customer references
2. **Maintains Data Integrity**: Prevents double-counting in aggregations
3. **Performance Optimization**: Reduces relationship complexity in VertiPaq engine

### **Alternative Approaches**

|   |   |   |
|---|---|---|
|Method|Pros|Cons|
|**Bridge Table**|Most reliable|Extra table in model|
|**M2M Relationship**|Simple setup|Risk of inaccurate results|
|**DAX Workarounds**|No schema changes|Complex measures|

### **Common Pitfalls**

1. Forgetting to remove duplicates in bridge table
2. Incorrect cross-filter direction
3. Using bridge table columns in visuals instead of original dimensions

---

### **Interview-Ready Enhancements**

### **1. Dynamic Bridge Table**

```Plain
// DAX calculated table for automatic updates
Bridge =
SUMMARIZE(
    Transactions,
    Transactions[CustomerName]
)
```

### **2. Handling Slowly Changing Dimensions**

```Plain
// Power Query script for SCD Type 2 bridge
= Table.AddColumn(
    #"Removed Duplicates",
    "ValidFrom",
    each DateTime.LocalNow(),
    type datetime
)
```

### **3. Advanced Relationship Management**

```Plain
// Measure controlling relationships dynamically
Safe Sales =
VAR UseBridge = [MeasureFlag] = "Bridge"  // Toggle via parameter
RETURN
    IF(
        UseBridge,
        CALCULATE(
            [Total Sales],
            USERELATIONSHIP(Bridge[CustomerName], Transactions[CustomerName])
        ),
        [Total Sales]
    )
```

---

### **Visualization Best Practices**

1. **Always use bridge table fields** for axis/legend fields
2. **Create hierarchy**: Bridge[Customer] → Customers[Region]
3. **Add data quality indicator**:
    
    ```Plain
    Data Quality =
    IF(
        COUNTROWS(Bridge) = DISTINCTCOUNT(Transactions[CustomerName]),
        "✅ Valid", "❌ Check Duplicates"
    )
    ```
    

---

### **Why Interviewers Value This Solution**

✅ Demonstrates **data modeling expertise**

✅ Shows understanding of **cardinality issues**

✅ Provides **production-ready solution**

✅ Includes **performance considerations**

**Pro Tip:** Mention how this approach scales for 3+ table chains (e.g., Customer → Bridge → Transactions → Products).