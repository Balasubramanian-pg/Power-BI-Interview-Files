## **Extracting Numbers from a Corrupted ID Column**  

### **Question**  
You have a dimension table with an **"ID"** column containing inconsistent and corrupted data (e.g., `"01_ProductA"`, `"02#ProductB"`, `"XYZ03ProductC"`). How would you extract **only the numeric part** (e.g., `01`, `02`, `03`) to create a clean ID column?  

**Illustrative Example**:  
| Corrupted ID    | Product   |  
|-----------------|----------|  
| 01_ProductA     | ProductA |  
| 02#ProductB     | ProductB |  
| XYZ03ProductC   | ProductC |  

**Expected Output**:  
| Clean ID | Product   |  
|----------|-----------|  
| 01       | ProductA  |  
| 02       | ProductB  |  
| 03       | ProductC  |  

> [!NOTE]  
> This problem tests your ability to clean and transform data using Power Query.  

### **Solution**  
Use **Power Query's "Column from Examples"** feature, which automatically detects patterns and generates the correct extraction logic.  

### **Code Explanation (Generated M Code)**  
```m  
= Table.AddColumn(  
    Source,  
    "Clean ID",  
    each Text.Select([Corrupted ID], {"0".."9"})  
)  
```  
- `**Text.Select()**` extracts only numeric characters (`0-9`) from the corrupted ID.  
- Power Query infers this logic when you manually provide examples (e.g., entering `01` for the first row).  

**Expected Output Explanation**:  
- The `Text.Select` function removes all non-numeric characters, leaving only the numbers.  
- This ensures consistency even if the numbers appear at different positions in the string.  

> [!TIP]  
> Use "Column from Examples" for quick and accurate data cleaning, especially with inconsistent formats.  

## **Scenario 2: Calculating MTD Revenue Excluding Today**  

### **Question**  
You have a fact table with **"Date"** and **"Revenue"** columns. How would you create a **DAX measure** that calculates **Month-to-Date (MTD) revenue but excludes today’s revenue**?  

**Illustrative Example**:  
| Date       | Revenue |  
|------------|---------|  
| 2024-06-01 | 5000    |  
| 2024-06-02 | 6000    |  
| ...        | ...     |  
| 2024-06-15 | 7000    |  

**Expected Output**:  
- If today is **June 15**, the measure should sum revenue from **June 1 to June 14**.  

> [!IMPORTANT]  
> This problem tests your ability to handle time-based calculations and dynamic filtering in DAX.  

### **Solution**  
Use a **custom DAX measure** with `EOMONTH`, `TODAY()`, and `FILTER` to exclude today’s data.  

### **Code Explanation (DAX Measure)**  
```dax  
Total MTD Excluding Today =  
VAR date_list = EOMONTH(TODAY(), -1) + 1  // First day of current month  
RETURN  
    CALCULATE(  
        SUM('Fact Table'[Revenue]),  
        FILTER(  
            'Fact Table',  
            'Fact Table'[Date] >= date_list &&  // From start of month  
            'Fact Table'[Date] < TODAY()        // Up to (but not including) today  
        )  
    )  
```  

**Expected Output Explanation**:  
- `**EOMONTH(TODAY(), -1) + 1**` gets the **first day of the current month**.  
- `**FILTER**` restricts the calculation to dates **before today**.  
- `**CALCULATE**` dynamically sums revenue in the filtered range.  

> [!TIP]  
> Use `EOMONTH` and `TODAY()` for dynamic date calculations in DAX.  

### **Final Notes**  

- **Scenario 1** demonstrates **Power Query’s pattern recognition** for data cleaning.  
- **Scenario 2** shows **advanced DAX time intelligence** for dynamic reporting.  

> [!IMPORTANT]  
> Understanding both Power Query and DAX is crucial for handling complex data transformation and calculation tasks in Power BI.  

This document provides clear, step-by-step solutions to common Power BI interview questions, addressing both data cleaning and time-based calculations.  

> [!TIP]  
> Practice these techniques with different datasets to reinforce your understanding of Power Query and DAX.  
