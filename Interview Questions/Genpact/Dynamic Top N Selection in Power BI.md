# **Dynamic Top N Selection in Power BI**  

**"Create a dynamic Top N selection feature in Power BI where:**  
- Users can select a number (e.g., Top 10) using a slicer.  
- The table should display the Top N products by sales.  
- Additionally, show an 'Others' row that aggregates the remaining products.  
- Total rows returned should be N+1 (e.g., if Top 10 selected, return 11 rows)."  

> [!NOTE]  
> This problem tests your ability to create dynamic, user-driven reports with DAX and Power BI features.  

## **Business Scenario**  

This is commonly used in executive dashboards where leadership wants to see:  
- Top performing products/customers/regions.  
- Everything else summarized as "Others" to maintain context.  
- Dynamic flexibility to change the Top N threshold.  

## **Step-by-Step Solution**  

### **Step 1: Create the Parameter Table**  

**Method A: Manual Table Creation**  
```dax  
TopN Parameter =  
DATATABLE(  
    "Top N Value", INTEGER,  
    "Top N Label", STRING,  
    {  
        {5, "Top 5"},  
        {10, "Top 10"},  
        {15, "Top 15"},  
        {20, "Top 20"}  
    }  
)  
```  

**Method B: Using Power Query (M Code)**  
```m  
let  
    Source = {5, 10, 15, 20, 25},  
    ConvertedToTable = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),  
    ChangedType = Table.TransformColumnTypes(ConvertedToTable,{{"Column1", Int64.Type}}),  
    RenamedColumns = Table.RenameColumns(ChangedType,{{"Column1", "TopN Value"}}),  
    AddedCustom = Table.AddColumn(RenamedColumns, "TopN Label", each "Top " & Text.From([TopN Value]))  
in  
    AddedCustom  
```  

> [!TIP]  
> Use Power Query for dynamic parameter table creation when the values are not fixed.  

### **Step 2: Create the Ranking Measure**  
```dax  
Product Rank =  
RANKX(  
    ALL(Products[ProductName]),  
    [Total Sales],  
    ,  
    DESC  
)  
```  

---

### **Step 3: Create the Selected Top N Value Measure**  
```dax  
Selected Top N =  
SELECTEDVALUE('TopN Parameter'[Top N Value], 10)  
```  

### **Step 4: Create the Dynamic Product Display Measure**  
```dax  
Dynamic Product Name =  
VAR SelectedTopN = [Selected Top N]  
VAR CurrentRank = [Product Rank]  
VAR CurrentProduct = SELECTEDVALUE(Products[ProductName])  
RETURN  
    IF(  
        CurrentRank <= SelectedTopN,  
        CurrentProduct,  
        IF(  
            CurrentRank = SelectedTopN + 1,  
            "Others",  
            BLANK()  
        )  
    )  
```  

> [!IMPORTANT]  
> This measure dynamically displays the product name or "Others" based on the rank.  

### **Step 5: Create the Sales Measure for the Table**  
```dax  
Dynamic Sales Display =  
VAR SelectedTopN = [Selected Top N]  
VAR CurrentRank = [Product Rank]  
RETURN  
    IF(  
        CurrentRank <= SelectedTopN,  
        [Total Sales],  
        IF(  
            CurrentRank = SelectedTopN + 1,  
            CALCULATE(  
                [Total Sales],  
                FILTER(  
                    ALL(Products[ProductName]),  
                    [Product Rank] > SelectedTopN  
                )  
            ),  
            BLANK()  
        )  
    )  
```  

> [!TIP]  
> The "Others" row aggregates sales from all products not in the Top N.  

### **Step 6: Alternative Approach - Using a Calculated Table**  

For better performance, create a calculated table:  
```dax  
Dynamic Top N Table =  
VAR SelectedTopN = SELECTEDVALUE('TopN Parameter'[Top N Value], 10)  
VAR TopNProducts =  
    TOPN(  
        SelectedTopN,  
        ADDCOLUMNS(  
            VALUES(Products[ProductName]),  
            "Sales", [Total Sales]  
        ),  
        [Sales],  
        DESC  
    )  
VAR OthersRow =  
    ROW(  
        "ProductName", "Others",  
        "Sales",  
        CALCULATE(  
            [Total Sales],  
            EXCEPT(  
                VALUES(Products[ProductName]),  
                SELECTCOLUMNS(TopNProducts, "ProductName", Products[ProductName])  
            )  
        )  
    )  
RETURN  
    UNION(  
        SELECTCOLUMNS(TopNProducts, "ProductName", Products[ProductName], "Sales", [Sales]),  
        OthersRow  
    )  
```  

> [!IMPORTANT]  
> Calculated tables are more efficient for large datasets as they precompute results.  

### **Step 7: Enhanced Version with Percentage**  

```dax  
Dynamic Sales with % =  
VAR SelectedTopN = [Selected Top N]  
VAR CurrentRank = [Product Rank]  
VAR TotalAllSales = CALCULATE([Total Sales], ALL(Products))  
VAR CurrentSales = [Dynamic Sales Display]  
RETURN  
    IF(  
        NOT ISBLANK(CurrentSales),  
        CurrentSales,  
        BLANK()  
    )  

Dynamic Sales % =  
VAR CurrentSales = [Dynamic Sales Display]  
VAR TotalSales = CALCULATE([Total Sales], ALL(Products))  
RETURN  
    IF(  
        NOT ISBLANK(CurrentSales),  
        DIVIDE(CurrentSales, TotalSales),  
        BLANK()  
    )  
```  
## **Implementation Steps in Power BI**  

1. **Data Model Setup**  
   - Import your sales data with Products table.  
   - Create the TopN Parameter table.  
   - Ensure proper relationships.  

2. **Create Measures**  
   - Add all the DAX measures above to your model.  
   - Test each measure individually.  

3. **Build the Visual**  
   - Create a table visual.  
   - Add `Dynamic Product Name` to Rows.  
   - Add `Dynamic Sales Display` and `Dynamic Sales %` to Values.  
   - Add TopN Parameter slicer.  

4. **Formatting**  
   - Sort the table by sales descending.  
   - Format numbers and percentages appropriately.  
   - Ensure "Others" row appears at the bottom.  

## **Advanced Variations**  

### **Multi-Level Grouping**  
```dax  
Dynamic Category Display =  
VAR SelectedTopN = [Selected Top N]  
RETURN  
    SWITCH(  
        TRUE(),  
        [Product Rank] <= SelectedTopN, SELECTEDVALUE(Products[ProductName]),  
        [Product Rank] = SelectedTopN + 1, "Others - " & SELECTEDVALUE(Products[Category]),  
        BLANK()  
    )  
```  

### **With Running Total**  
```dax  
Dynamic Cumulative Sales =  
VAR SelectedTopN = [Selected Top N]  
VAR CurrentRank = [Product Rank]  
RETURN  
    IF(  
        CurrentRank <= SelectedTopN + 1,  
        CALCULATE(  
            [Total Sales],  
            FILTER(  
                ALL(Products[ProductName]),  
                [Product Rank] <= CurrentRank  
            )  
        )  
    )  
```  

## **Common Interview Follow-up Questions**  

1. **"How would you handle ties in ranking?"**  
   - Use `RANKX` with tie-breaking logic or `DENSE_RANK` equivalent.  

2. **"What if we want Top N by different measures?"**  
   - Create a parameter for measure selection alongside Top N.  

3. **"How to optimize performance for large datasets?"**  
   - Use calculated tables, avoid complex nested `CALCULATE` statements.  
   - Consider aggregations at the data source level.  

4. **"How to make this work with multiple categories?"**  
   - Add category context to all measures.  
   - Use `ALLEXCEPT` instead of `ALL` where appropriate.  

## **Key Interview Points to Mention**  

1. **Performance Considerations**: Explain why calculated tables are better for large datasets.  
2. **User Experience**: Discuss slicer placement and visual formatting.  
3. **Error Handling**: Handle cases where `TopN > total products`.  
4. **Scalability**: How the solution works with different data volumes.  
5. **Business Value**: Explain why the "Others" category is important for executive reporting.  

> [!TIP]  
> Highlight the flexibility and scalability of the solution to impress interviewers.  

This document provides a comprehensive solution to the dynamic Top N selection problem in Power BI, covering both basic and advanced implementations. It’s designed to help candidates understand and articulate the solution effectively in interviews.  

> [!TIP]  
> Practice this pattern with different datasets to reinforce your understanding of dynamic calculations and ranking in DAX.  
