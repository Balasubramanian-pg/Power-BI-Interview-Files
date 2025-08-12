### Scenario 1: Extracting Numbers from Corrupted ID Column

**Question:**  
How can we extract only the numbers from a corrupted 'ID' column that contains mixed characters and inconsistent patterns?

**Table Schema:**

```Markdown
 | ID (Corrupted) | Product   |
 |----------------|-----------|
 | ABC01XYZ       | ProductA  |
 | DEF02GHI       | ProductB  |
 | JKL03MNO       | ProductC  |
```

**Solution Explained:**

1. **Column from Examples**:
    - In Power Query, you can provide examples of the desired output. For instance, for the first row, you might type "01" as the desired output.
    - Power Query will then try to generalize the pattern and apply it to the rest of the rows.
2. **Underlying Function**:
    - The Text.Select function is likely used here. It's a function that extracts specific characters based on a pattern.

**Expected Outcome:**  
The cleaned 'ID' column should look like this:

```Markdown
  ID (Cleaned) | Product   |
 |--------------|-----------|
 | 01           | ProductA  |
 | 02           | ProductB  |
 | 03           | ProductC  |
```

---

### Scenario 2: Calculating Current Month's Total Revenue Excluding Today's Date

**Question:**  
How can we write a DAX measure to calculate the total revenue for the current month, excluding today's date?

**Table Schema:**

```Markdown
 Copy
  Date       | Revenue |
 |------------|---------|
 | 2023-10-01 | 2000    |
 | 2023-10-02 | 3000    |
 | 2023-10-03 | 1000    |
 | ...        | ...     |
 | 2023-10-15 | 5000    | (Assuming today is 2023-10-15)
```

**Solution Explained:**

1. **Problem with TOTALMTD**:
    - The TOTALMTD function includes today's date, which is not desired.
2. **Robust DAX Measure**:
    - A new measure called "total MTD final" is created.
    - It uses a variable `date_list` to define the date range.
    - The EOMONTH function is used to get the end of the previous month.
    - Adding +1 to the EOMONTH result brings the date to the first day of the current month.
    - The CALCULATE function with SUM(Revenue) and a FILTER is used.
    - The filter conditions are:
        - Date should be greater than or equal to the first day of the current month.
        - Date should be less than today's date.

**DAX Code:**

```Plain
 Copy
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

**Expected Outcome:**  
The measure should return the sum of revenue for all dates in the current month up to, but not including, today's date. For example, if today is 2023-10-15, the measure should sum revenue from 2023-10-01 to 2023-10-14.

  

Here are three alternative DAX measures that achieve the same output as the original measure:

1. Using TOTALMTD and subtracting today's revenue:

```Plain
 Copy
total MTD final Alt1 =
VAR TotalMTD = TOTALMTD(SUM('Fact Table'[Revenue]), 'Fact Table'[Date])
VAR TodaysRevenue = CALCULATE(SUM('Fact Table'[Revenue]), 'Fact Table'[Date] = TODAY())
RETURN
TotalMTD - TodaysRevenue
```

1. Using DATESMTD with a filter to exclude today:

```Plain
 Copy
total MTD final Alt2 =
CALCULATE(
    SUM('Fact Table'[Revenue]),
    DATESMTD('Fact Table'[Date]),
    'Fact Table'[Date] < TODAY()
)
```

1. Using YEAR and MONTH to filter dates:

```Plain
 Copy
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

These measures provide different approaches to achieve the same result, demonstrating flexibility in DAX to solve the same problem in multiple ways.