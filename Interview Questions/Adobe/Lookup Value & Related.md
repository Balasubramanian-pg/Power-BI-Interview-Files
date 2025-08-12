# **LOOKUPVALUE vs RELATED: Key Differences**  

| **Feature**         | **`LOOKUPVALUE`**                          | **`RELATED`**                          |  
|----------------------|--------------------------------------------|----------------------------------------|  
| **Relationship**     | Works without table relationships          | Requires active relationship           |  
| **Performance**      | Slower (scans entire table)                | Faster (uses relationship)             |  
| **Use Case**         | Ad-hoc lookups across tables               | Standard star schema queries           |  
| **Syntax**           | `LOOKUPVALUE(result, col, search)`         | `RELATED(column)`                      |  

> [!TIP]  
> Choose `LOOKUPVALUE` for flexibility and `RELATED` for performance in relational models.  

## **LOOKUPVALUE Deep Dive**  

### **Syntax Structure**  

```dax  
LOOKUPVALUE(  
    result_columnName,  // Column to return (e.g., Discount[Discount%])  
    search_columnName1, // Matching column (e.g., Discount[Product])  
    search_value1,      // Value to match (e.g., Product[ProductName])  
    [search_columnName2, search_value2,...] // Optional additional conditions  
)  
```  

### **Practical Example from Video**  

**Scenario**:  
Add discount percentages from a standalone `Discount` table to a `Product` table without creating a relationship.  

**Solution**:  
```dax  
// Calculated column in Product table  
Discount % =  
LOOKUPVALUE(  
    Discount[Discount%],  // Column to fetch  
    Discount[Product],    // Column to match in Discount table  
    Product[ProductName]  // Value to match from current row  
)  
```  

> [!IMPORTANT]  
> This mimics Excel’s `VLOOKUP` but is more flexible and context-aware.  

### **How It Works**  

1. **For each row in `Product` table**:  
   - Searches `Discount` table for a matching product name.  
   - Returns the corresponding `Discount%` value.  

### **Advanced Considerations**  

1. **Error Handling**  
   Add fallback logic for missing matches:  
   ```dax  
   Discount % =  
   VAR DiscountValue = LOOKUPVALUE(Discount[Discount%], Discount[Product], Product[ProductName])  
   RETURN IF(ISBLANK(DiscountValue), 0, DiscountValue)  
   ```  

2. **Multi-Column Lookups**  
   ```dax  
   // Match on product + region  
   LOOKUPVALUE(  
       Discount[Discount%],  
       Discount[Product], Product[ProductName],  
       Discount[Region], Sales[Region]  
   )  
   ```  

3. **Performance Optimization**  
   - **Avoid** in large datasets (>1M rows).  
   - **Prefer `RELATED`** when relationships exist.  
   - **Consider merging tables** in Power Query instead.  

> [!WARNING]  
> `LOOKUPVALUE` can be slow on large datasets due to row-by-row operations.  

### **When to Use LOOKUPVALUE**  

**No Relationship** scenarios  
**Ad-hoc analysis** requiring flexible lookups  
**Complex conditions** (multiple column matches)  

### **When to Avoid**  

**Large datasets** (use relationships + `RELATED`)  
**Frequent calculations** (creates row-by-row operations)  


### **Pro Tip for Interviews**  

Demonstrate understanding of:  
1. **Context Transition**: `LOOKUPVALUE` respects row context.  
2. **Alternatives**: When to use `RELATED` vs `LOOKUPVALUE`.  
3. **Performance Tradeoffs**: Explain why `RELATED` is faster.  

> [!TIP]  
> Highlight scenarios where `LOOKUPVALUE` is the only solution (e.g., no relationships).  

This document provides a clear comparison between `LOOKUPVALUE` and `RELATED`, along with practical examples and best practices. It’s designed to help candidates understand when and how to use each function effectively in Power BI.  

> [!TIP]  
> Practice creating calculated columns and measures using `LOOKUPVALUE` and `RELATED` to reinforce your understanding.  
