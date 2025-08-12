## **Combining Text from Multiple Rows**  

**Problem**:  
Given a table of students and their subjects, combine all subjects for each student into a single comma-separated string.  

**Input Example**:  
| Name  | Subject |  
|-------|---------|  
| John  | English |  
| John  | Physics |  
| John  | Maths   |  

**Expected Output**:  
| Name  | Combined Subjects       |  
|-------|-------------------------|  
| John  | English, Physics, Maths |  

> [!NOTE]  
> This problem tests your ability to use Power Query for data transformation.  

### **Solution (Power Query M Code)**  

1. **Group By Name**:  
   - Select the table → **Transform** → **Group By**.  
   - Group column: `Name`.  
   - Operation: **All Rows** (creates a nested table).  

2. **Modify M Code**:  
   ```m  
   = Table.Group(#"Previous Step", {"Name"}, {{"Combined Subjects", each Text.Combine([Subject], ", "), type text}})  
   ```  
   - Replaces `List.Sum` (default for numbers) with `Text.Combine()`.  
   - `", "` adds a comma separator.  

> [!TIP]  
> `Text.Combine()` is ideal for merging text values from lists.  

## **Question 2: Grouping Products Without DAX**  

**Problem**:  
Categorize iPhone models into "Old" (11/12/13) and "New" (14/15) groups.  

**Input Example**:  
```Plain  
Product  
---------  
iPhone 11  
iPhone 12  
iPhone 13  
iPhone 14  
iPhone 15  
```  

**Expected Output**:  
| Category    | Products            |  
|-------------|---------------------|  
| Old iPhones | iPhone 11, 12, 13   |  
| New iPhones | iPhone 14, 15       |  

> [!NOTE]  
> This problem tests your ability to use Power BI’s UI features for ad-hoc grouping.  

### **Solution (Power BI Data Groups)**  

1. **Select Products**:  
   - In **Data View**, Ctrl+click to select:  
     - iPhone 11, 12, 13 → Right-click → **Group**.  
     - iPhone 14, 15 → Right-click → **Group**.  
2. **Rename Groups**:  
   - Change default names to "Old iPhones" and "New iPhones".  

> [!TIP]  
> Use **Data Groups** for static categories to avoid writing DAX.  

### **Comparison of Methods**  

| **Task**               | **Tool Used** | **Key Function**          |  
|------------------------|---------------|---------------------------|  
| Text Concatenation     | Power Query  | `Text.Combine()`          |  
| Product Grouping       | Power BI UI  | **Data Groups** feature   |  

### **Pro Tips for Interviews**  

1. **For text merging**, always prefer `Text.Combine()` over `List.Sum`.  
2. **Use Data Groups** when:  
   - Categories are static (e.g., product generations).  
   - Avoiding DAX simplifies the solution.  

> [!IMPORTANT]  
> Demonstrating knowledge of both Power Query and Power BI UI features shows versatility.  

### **DAX Alternative for Grouping**  

If you need a DAX solution for grouping:  
```dax  
Category =  
SWITCH(  
    TRUE(),  
    'Table'[Product] IN {"iPhone 11", "iPhone 12", "iPhone 13"}, "Old iPhones",  
    'Table'[Product] IN {"iPhone 14", "iPhone 15"}, "New iPhones",  
    "Other"  
)  
```  

> [!TIP]  
> Use `SWITCH` for cleaner conditional logic in DAX.  

This document provides clear, step-by-step solutions to common Power BI interview questions, addressing both Power Query and DAX techniques.  

> [!TIP]  
> Practice these methods with different datasets to reinforce your understanding of data transformation and grouping in Power BI.  
