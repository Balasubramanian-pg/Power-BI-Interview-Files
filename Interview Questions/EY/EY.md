# **Aggregating Transaction Types**  

## **Problem Statement**  

**Given**:  
A transaction table with:  
- **Date**  
- **Transaction Type 1**  
- **Transaction Type 2**  

**Requirement**:  
Create a new table showing:  
1. **Date**  
2. **Unique transaction types** (from both columns)  
3. **Count of each transaction type**  

> [!NOTE]  
> This problem tests your ability to handle multiple columns, aggregate data, and use DAX for table manipulation.  


### **Sample Input**  

| Date        | Transaction Type 1 | Transaction Type 2 |  
|-------------|--------------------|--------------------|  
| 2024-01-01  | A                  | B                  |  
| 2024-01-01  | A                  | C                  |  
| 2024-01-01  | B                  | D                  |  
| 2024-01-01  | C                  | E                  |  
| 2024-01-01  | D                  | A                  |  


### **Solution Approach**  

1. **Create Summary Tables**:  
   - Use `SUMMARIZE` to group by Date and each Transaction Type column.  
   - This ensures unique combinations of Date and Transaction Type.  

2. **Combine Results**:  
   - Use `UNION` to merge the two summary tables into one.  

3. **Create Final Table**:  
   - Use `SELECTCOLUMNS` to format the output and rename columns.  

4. **Add Count Measure**:  
   - Use `COUNTROWS` with `FILTER` to count occurrences of each transaction type.  

> [!TIP]  
> `UNION` preserves duplicates, which is essential for accurate counting.  

### **DAX Implementation**  

```dax  
result =  
VAR Combined =  
    UNION(  
        SUMMARIZE(Sheet1, Sheet1[Date], Sheet1[Transaction Type 1]),  
        SUMMARIZE(Sheet1, Sheet1[Date], Sheet1[Transaction Type 2])  
    )  
RETURN  
    SELECTCOLUMNS(  
        Combined,  
        "Date", [Date],  
        "Transaction Type", [Transaction Type 1]  
    )  
```  

**Count Measure**:  
```dax  
Transaction Count =  
COUNTROWS(  
    FILTER(  
        result,  
        result[Transaction Type] = SELECTEDVALUE(result[Transaction Type])  
    )  
)  
```  

> [!IMPORTANT]  
> The `FILTER` function ensures the count is calculated per selected transaction type.  


### **Expected Output**  

| Date        | Transaction Type | Count |  
|-------------|------------------|-------|  
| 2024-01-01  | A                | 3     |  
| 2024-01-01  | B                | 2     |  
| 2024-01-01  | C                | 2     |  
| 2024-01-01  | D                | 2     |  
| 2024-01-01  | E                | 1     |  

### **Key Insights**  

1. **`SUMMARIZE`**: Creates distinct groupings by Date and Transaction Type.  
2. **`UNION`**: Combines results while preserving duplicates (needed for accurate counting).  
3. **`COUNTROWS` with `FILTER`**: Calculates occurrences per transaction type.  

> [!TIP]  
> Use `SUMMARIZE` to handle grouping efficiently, especially with large datasets.  

### **Alternative Solution Using Power Query**  

1. **Unpivot Both Transaction Type Columns**:  
   - Transform the table to have a single "Transaction Type" column.  
2. **Group by Date and Transaction Type**:  
   - Aggregate the unpivoted data to count occurrences.  

> [!NOTE]  
> Power Query is ideal for data transformation tasks like unpivoting.  


### **Final Thoughts**  

This solution efficiently combines transaction types from both columns, groups by date and transaction type, and counts occurrences. The use of `UNION` to combine the two sets of transaction types is the key insight, allowing uniform treatment in subsequent steps.  

> [!TIP]  
> Practice this pattern with different datasets to reinforce your understanding of DAX and data aggregation.  


This document provides a clear, step-by-step explanation of aggregating transaction types in Power BI, addressing common interview questions and challenges.  

> [!TIP]  
> Experiment with variations (e.g., adding more transaction type columns) to deepen your understanding of DAX and data manipulation.  
