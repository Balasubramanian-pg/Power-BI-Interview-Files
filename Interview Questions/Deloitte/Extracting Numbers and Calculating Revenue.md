# **Extracting Numbers and Calculating Revenue**  

---

## **Scenario 1: Extracting Numbers from Corrupted ID Column**  

### **Problem Statement**  
How can we extract only the numbers from a corrupted 'ID' column that contains mixed characters and inconsistent patterns?  

**Table Schema**:  
```markdown  
| ID (Corrupted) | Product   |  
|----------------|-----------|  
| ABC01XYZ       | ProductA  |  
| DEF02GHI       | ProductB  |  
| JKL03MNO       | ProductC  |  
```  

> [!NOTE]  
> This problem tests your ability to clean and transform data using Power Query.  

### **Solution Explained**  
1. **Column from Examples**:  
   - In Power Query, provide examples of the desired output (e.g., "01" for "ABC01XYZ").  
   - Power Query uses the `Text.Select` function to generalize the pattern and apply it to all rows.  

2. **Expected Outcome**:  
```markdown  
| ID (Cleaned) | Product   |  
|--------------|-----------|  
| 01           | ProductA  |  
| 02           | ProductB  |  
| 03           | ProductC  |  
```  

> [!TIP]  
> Use the **Column from Examples** feature in Power Query for quick and accurate data cleaning.  

---

## **Scenario 2: Calculating Current Month's Total Revenue Excluding Today's Date**  

### **Problem Statement**  
How can we write a DAX measure to calculate the total revenue for the current month, excluding today's date?  

**Table Schema**:  
```markdown  
| Date       | Revenue |  
|------------|---------|  
| 2023-10-01 | 2000    |  
| 2023-10-02 | 3000    |  
| 2023-10-03 | 1000    |  
| ...        | ...     |  
| 2023-10-15 | 5000    | (Assuming today is 2023-10-15)  
```  

> [!IMPORTANT]  
> This problem tests your ability to handle time-based calculations and filter data dynamically in DAX.  

### **Solution Explained**  
1. **Issue with `TOTALMTD`**:  
   - `TOTALMTD` includes today's date, which is not desired.  

2. **Robust DAX Measure**:  
   - Use `CALCULATE`, `FILTER`, and date functions to exclude today's date.  

**DAX Code**:  
```dax  
total MTD final =  
VAR date_list = EOMONTH(TODAY(), -1) + 1  
RETURN  
    CALCULATE(  
        SUM(Revenue),  
        FILTER(  
            'Fact Table',  
            'Fact Table'[Date] >= date_list &&  
            'Fact Table'[Date] < TODAY()  
        )  
    )  
```  

**Expected Outcome**:  
The measure sums revenue from the first day of the current month up to, but not including, today's date. For example, if today is 2023-10-15, it sums revenue from 2023-10-01 to 2023-10-14.  

> [!TIP]  
> Use `EOMONTH` and `TODAY()` to dynamically define the date range.  

---

### **Alternative DAX Measures**  

1. **Using `TOTALMTD` and Subtracting Today's Revenue**:  
```dax  
total MTD final Alt1 =  
VAR TotalMTD = TOTALMTD(SUM('Fact Table'[Revenue]), 'Fact Table'[Date])  
VAR TodaysRevenue = CALCULATE(SUM('Fact Table'[Revenue]), 'Fact Table'[Date] = TODAY())  
RETURN  
    TotalMTD - TodaysRevenue  
```  

2. **Using `DATESMTD` with a Filter to Exclude Today**:  
```dax  
total MTD final Alt2 =  
CALCULATE(  
    SUM('Fact Table'[Revenue]),  
    DATESMTD('Fact Table'[Date]),  
    'Fact Table'[Date] < TODAY()  
)  
```  

3. **Using `YEAR` and `MONTH` to Filter Dates**:  
```dax  
total MTD final Alt3 =  
VAR CurrentYear = YEAR(TODAY())  
VAR CurrentMonth = MONTH(TODAY())  
VAR TodayDate = TODAY()  
RETURN  
    CALCULATE(  
        SUM('Fact Table'[Revenue]),  
        FILTER(  
            'Fact Table',  
            YEAR('Fact Table'[Date]) = CurrentYear &&  
            MONTH('Fact Table'[Date]) = CurrentMonth &&  
            'Fact Table'[Date] < TodayDate  
        )  
    )  
```  

> [!IMPORTANT]  
> These alternatives demonstrate flexibility in DAX to solve the same problem in multiple ways.  

---

This document provides clear, step-by-step solutions to common Power BI challenges, making it ideal for interview preparation and real-world problem-solving.  

> [!TIP]  
> Practice these techniques with different datasets to reinforce your understanding of data cleaning and time-based calculations in Power BI.  
