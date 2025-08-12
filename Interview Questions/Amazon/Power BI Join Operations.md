# Power BI Join Operations - Interview Question & Solution  

## **Question**  

**"Explain the different types of joins available in Power Query Editor in Power BI and demonstrate their behavior with null values. How does null handling in Power BI differ from SQL?"**  

> [!NOTE]  
> This question tests your understanding of Power Query joins and the unique way Power BI handles null values.  

---

## **Key Concept: Null Value Behavior in Power BI**  

**Critical Interview Point:** In Power BI Power Query, `null = null` evaluates to `TRUE`, which is different from SQL where `null ≠ null`.  

### **Proof of Null Equality**  

**Test Table: `null_verify`**  

| Column A | Column B | Comparison Result |  
|----------|----------|-------------------|  
| 10       | 10       | 1 (Equal)         |  
| 1        | 2        | 0 (Not Equal)     |  
| null     | null     | 1 (Equal) ← **Key Point!** |  
| 2        | 3        | 0 (Not Equal)     |  

**Custom Column Formula:**  
```m  
if [Column A] = [Column B] then 1 else 0  
```  

> [!IMPORTANT]  
> Power BI treats `null = null` as `TRUE`, which is a fundamental difference from SQL.  

---

## **Sample Data Setup**  

**Left Table:**  
```Plain  
4  
5  
5  
null  
10  
10  
15  
```  

**Right Table:**  
```Plain  
5  
null  
6  
null  
10  
12  
```  

**Common Values**: 5, null, 10 (these will match in joins).  

---

## **Join Operations & Expected Results**  

### **1. Inner Join**  
**Definition**: Returns only matching records from both tables.  
**Expected Output**:  
```Plain  
5  
5  
null  
null  
10  
10  
```  

> [!NOTE]  
> Cartesian product applies: 2 records of '5' in left × 1 record of '5' in right = 2 results.  

### **2. Left Outer Join**  
**Definition**: All records from the left table + matching records from the right table.  
**Expected Output**:  
```Plain  
4      ← Left table only  
5  
5  
null  
null  
10  
10  
15     ← Left table only  
```  

### **3. Right Outer Join**  
**Definition**: All records from the right table + matching records from the left table.  
**Expected Output**:  
```Plain  
5  
5  
null  
null  
10  
10  
6      ← Right table only  
12     ← Right table only  
```  

### **4. Left Anti Join**  
**Definition**: Records from the left table that don't have matches in the right table.  
**Expected Output**:  
```Plain  
4  
15  
```  

### **5. Right Anti Join**  
**Definition**: Records from the right table that don't have matches in the left table.  
**Expected Output**:  
```Plain  
6  
12  
```  

### **6. Full Outer Join**  
**Definition**: All records from both tables (matching + non-matching).  
**Expected Output**:  
```Plain  
4      ← Left anti  
5      ← Matching  
5      ← Matching  
null   ← Matching  
null   ← Matching  
10     ← Matching  
10     ← Matching  
15     ← Left anti  
6      ← Right anti  
12     ← Right anti  
```  

---

## **Power Query M Code Example**  

```m  
// Create custom column to test null equality  
= Table.AddColumn(  
    Source,  
    "Comparison",  
    each if [Column A] = [Column B] then 1 else 0  
)  

// Merge queries syntax  
= Table.NestedJoin(  
    LeftTable,  
    {"Column"},  
    RightTable,  
    {"Column"},  
    "RightTable",  
    JoinKind.Inner  // or LeftOuter, RightOuter, LeftAnti, RightAnti, FullOuter  
)  
```  

---

## **Interview Key Points**  

1. **Null Behavior**: Unlike SQL, Power BI treats `null = null` as `TRUE`.  
2. **Join Results**: This affects all join operations, especially when null values are present.  
3. **Cartesian Product**: Matching records create Cartesian products (e.g., 2×1=2 results).  
4. **Practical Impact**: Join results in Power BI may differ significantly from equivalent SQL operations.  

> [!WARNING]  
> Be aware of null handling differences when migrating SQL-based solutions to Power BI.  

---

## **Why This Matters**  

This difference in null handling can lead to unexpected results when migrating from SQL-based solutions to Power BI, making it a crucial interview topic for Power BI developers.  

> [!TIP]  
> Practice join operations with null values in Power Query to reinforce your understanding of this behavior.  

---

This document provides a clear explanation of Power BI join operations and their behavior with null values, highlighting key differences from SQL. It’s designed to help candidates understand and articulate these concepts effectively in interviews.  

> [!TIP]  
> Use sample datasets to experiment with different join types and observe how null values affect the results.  

---
