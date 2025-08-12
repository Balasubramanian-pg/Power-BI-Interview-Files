  

### **LOOKUPVALUE vs RELATED: Key Differences**

|   |   |   |
|---|---|---|
|Feature|`LOOKUPVALUE`|`RELATED`|
|**Relationship**|Works without table relationships|Requires active relationship|
|**Performance**|Slower (scans entire table)|Faster (uses relationship)|
|**Use Case**|Ad-hoc lookups across tables|Standard star schema queries|
|**Syntax**|`LOOKUPVALUE(result,col,search)`|`RELATED(column)`|

---

### **LOOKUPVALUE Deep Dive**

### **Syntax Structure**

```Plain
LOOKUPVALUE(
    result_columnName,  // Column to return (e.g., Discount[Discount%])
    search_columnName1, // Matching column (e.g., Discount[Product])
    search_value1,      // Value to match (e.g., Product[ProductName])
    [search_columnName2, search_value2,...] // Optional additional conditions
)
```

### **Practical Example from Video**

**Scenario:**

Add discount percentages from a standalone `Discount` table to a `Product` table without creating a relationship.

**Solution:**

```Plain
// Calculated column in Product table
Discount % =
LOOKUPVALUE(
    Discount[Discount%],  // Column to fetch
    Discount[Product],    // Column to match in Discount table
    Product[ProductName]  // Value to match from current row
)
```

### **How It Works**

1. For each row in `Product` table:
    - Searches `Discount` table for matching product name
    - Returns corresponding `Discount%` value
    - Similar to Excel's `VLOOKUP` but more flexible

---

### **Advanced Considerations**

1. **Error Handling**
    
    Add fallback logic for missing matches:
    
    ```Plain
    Discount % =
    VAR DiscountValue = LOOKUPVALUE(Discount[Discount%], Discount[Product], Product[ProductName])
    RETURN IF(ISBLANK(DiscountValue), 0, DiscountValue)
    ```
    
2. **Multi-Column Lookups**
    
    ```Plain
    // Match on product + region
    LOOKUPVALUE(
        Discount[Discount%],
        Discount[Product], Product[ProductName],
        Discount[Region], Sales[Region]
    )
    ```
    
3. **Performance Optimization**
    - Avoid in large datasets (>1M rows)
    - Prefer `RELATED` when relationships exist
    - Consider merging tables in Power Query instead

---

### **When to Use LOOKUPVALUE**

✅ **No Relationship** scenarios

✅ **Ad-hoc analysis** requiring flexible lookups

✅ **Complex conditions** (multiple column matches)

### **When to Avoid**

❌ **Large datasets** (use relationships + `RELATED`)

❌ **Frequent calculations** (creates row-by-row operations)

---

### **Pro Tip for Interviews**

Demonstrate understanding of:

1. **Context Transition**: LOOKUPVALUE respects row context
2. **Alternatives**: When you'd use `RELATED` vs `LOOKUPVALUE`
3. **Performance Tradeoffs**

Would you like a practical exercise to test your LOOKUPVALUE skills? 🚀