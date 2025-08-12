# **Combining Disconnected Tables with Index Columns**  

## **Problem Statement**  

**Scenario**:  
You have two tables without a common key:  
- **Customer Table** (Name, Email)  
- **Address Table** (Address, City, State)  

**Challenge**:  
Combine them meaningfully when no natural relationship exists.  

> [!NOTE]  
> This problem tests your ability to handle data integration and create surrogate keys in Power BI.  

## **Step-by-Step Solution**  

### **1. Create Index Columns in Power Query**  

**For Customer Table**:  
1. Go to **Transform Data** → Select Customer Table.  
2. **Add Column** → **Index Column** → From 1.  
3. Result: New column `Index` with values 1, 2, 3...  

**For Address Table**:  
Repeat the same steps to add an identical index column.  

> [!TIP]  
> Index columns serve as surrogate keys for joining tables.  

### **2. Merge Tables Using Index**  
1. Select **Customer Table** → **Home** → **Merge Queries**.  
2. Choose **Address Table** as the second table.  
3. Select **Index** columns in both tables.  
4. Join Kind: **Inner Join** (for matching records only).  

### **3. Expand Merged Columns**  
1. Click the expand icon (↗) on the new merge column.  
2. Select only needed address fields (uncheck "Original Column Name").  
3. Result: Combined table with customer + address data.  

## **Key Technical Considerations**  

### **Why This Works**  
- **Index columns act as surrogate keys**.  
- **Auto-increment ensures new records get unique IDs**.  
- **Preserves data integrity when sources update**.  

### **Alternative Methods**  

| **Method**          | **Pros**                      | **Cons**                          |  
|---------------------|-------------------------------|-----------------------------------|  
| **Index Columns**   | Simple, automatic             | Assumes row order matches         |  
| **Custom Key Logic**| More precise                 | Requires complex M code           |  
| **Power Query Merge**| No model changes              | Less dynamic for updates          |  

> [!IMPORTANT]  
> Index columns are the most straightforward method for this scenario.  

### **M Code Implementation**  

```m  
// Customer Table  
let  
    Source = Excel.CurrentWorkbook(){[Name="Customers"]}[Content],  
    AddIndex = Table.AddIndexColumn(Source, "Index", 1, 1, Int64.Type)  
in  
    AddIndex  

// Address Table (similar)  
```  

## **Interview-Ready Enhancements**  

### **1. Handling Data Skew**  
```m  
// If tables might have mismatched rows  
try Table.AddIndexColumn(...) otherwise Table.AddColumn(..., "Index", each null)  
```  

### **2. Dynamic Index Assignment**  
```m  
// For variable-length tables  
Index = List.Generate(  
    () => 0,  
    each _ < Table.RowCount(Source),  
    each _ + 1  
)  
```  

### **3. Error Prevention**  
- Add data validation step:  
  `if Table.RowCount(Customers) = Table.RowCount(Addresses) then...`  

> [!TIP]  
> Error handling ensures robustness in production environments.  

## **Visual Representation**  

**Before Merge**:  
| Customers       | Addresses       |  
|-----------------|-----------------|  
| Name, Email     | Address, City   |  

**After Merge**:  
| Index | Name  | Email       | Address | City |  
|-------|-------|-------------|--------|------|  
| 1     | John  | john@x.com  | 123 Main| NY   |  

## **Why Interviewers Love This**  

Demonstrates **data modeling creativity**.  
Shows **Power Query expertise**.  
Solves real-world **ETL challenges**.  
Highlights understanding of **surrogate keys**.  

**Pro Tip**: Mention how you'd extend this for slowly changing dimensions (SCDs) in a follow-up question.  

---

This document provides a comprehensive solution to combining disconnected tables in Power BI, addressing common interview questions and challenges.  

> [!TIP]  
> Practice this technique with different datasets to reinforce your understanding of data integration and surrogate keys.  
