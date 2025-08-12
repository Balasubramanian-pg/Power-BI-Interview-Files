# Power BI Running Total (Cumulative Total) - Interview Question & Solution

## Interview Question

**"Can you write the DAX code to calculate a running total (cumulative total)? How would you create a measure that shows the cumulative sales up to a particular date?"**

## What is Running Total?

**Running Total (Cumulative Total)** is the sum of all values from the beginning up to the current row/date. It accumulates values progressively.

### Example:

```Plain
Year | Sales Value | Running Total
-----|-------------|---------------
2020 | 890         | 890           (890)
2021 | 275         | 1165          (890 + 275)
2022 | 1500        | 2665          (890 + 275 + 1500)
```

## Sample Data Setup

### Sales Sample Table:

```Plain
Product | Order Date | Sales Value
--------|------------|------------
A       | 2020-03-15 | 500
B       | 2020-06-20 | 390
C       | 2021-02-10 | 275
D       | 2022-01-05 | 800
E       | 2022-08-12 | 700
```

### Calendar Table:

```Plain
Date       | Year | Month
-----------|------|------
2020-01-01 | 2020 | 1
2020-01-02 | 2020 | 1
...        | ...  | ...
2022-12-31 | 2022 | 12
```

### Model Relationship:

- **Sales Sample Table** ↔ **Calendar Table**
- **Relationship:** One-to-Many
- **Key:** Date columns

## Solution Code

### ❌ Incorrect Approach (Common Mistake):

```Plain
Running Total =
CALCULATE(
    SUM(Sales[Sales Value]),
    FILTER(
        Calendar,
        Calendar[Date] <= MAX(Calendar[Date])
    )
)
```

**Problem:** This doesn't ignore existing filters, so it returns the same values as regular sum.

### ✅ Correct Solution:

```Plain
Running Total =
CALCULATE(
    SUM(Sales[Sales Value]),
    FILTER(
        ALL(Calendar),
        Calendar[Date] <= MAX(Calendar[Date])
    )
)
```

## Expected Output

### Year-wise Breakdown:

```Plain
Year | Sales Value | Running Total | Calculation
-----|-------------|---------------|------------------
2020 | 890         | 890           | 890
2021 | 275         | 1,165         | 890 + 275
2022 | 1,500       | 2,665         | 890 + 275 + 1,500
```

### Visual Representation:

```Plain
2020: ████████████████████ 890
2021: ████████████████████████████████ 1,165
2022: ████████████████████████████████████████████████████ 2,665
```

## Code Breakdown

### 1. CALCULATE Function

```Plain
CALCULATE(
    SUM(Sales[Sales Value]),  -- Expression to calculate
    FILTER(...)               -- Filter context
)
```

### 2. SUM Function

```Plain
SUM(Sales[Sales Value])  -- Sums the sales values
```

### 3. FILTER Function

```Plain
FILTER(
    ALL(Calendar),                    -- Table to filter
    Calendar[Date] <= MAX(Calendar[Date])  -- Filter condition
)
```

### 4. ALL Function (Critical Part)

```Plain
ALL(Calendar)  -- Ignores existing filters and returns all calendar rows
```

### 5. Filter Condition

```Plain
Calendar[Date] <= MAX(Calendar[Date])  -- Include all dates up to current row's date
```

## Why ALL() Function is Critical

### Without ALL():

- Existing filters remain active
- Only current row's date is considered
- Result = Same as regular SUM (no cumulative effect)

### With ALL():

- Ignores all existing filters on Calendar table
- Considers all rows in Calendar table
- Applies new filter condition (dates <= current date)
- Result = True cumulative total

## Step-by-Step Logic

1. **ALL(Calendar)** - Remove all existing filters from Calendar table
2. **Calendar[Date] <= MAX(Calendar[Date])** - Keep only dates up to current row's maximum date
3. **SUM(Sales[Sales Value])** - Sum sales values for the filtered date range
4. **CALCULATE** - Apply the filter context and calculate the result

## Alternative Approaches

### Using SUMX with FILTER:

```Plain
Running Total Alt =
SUMX(
    FILTER(
        ALL(Calendar),
        Calendar[Date] <= MAX(Calendar[Date])
    ),
    CALCULATE(SUM(Sales[Sales Value]))
)
```

### Using Variables for Clarity:

```Plain
Running Total Clear =
VAR CurrentDate = MAX(Calendar[Date])
VAR FilteredDates =
    FILTER(
        ALL(Calendar),
        Calendar[Date] <= CurrentDate
    )
RETURN
    CALCULATE(
        SUM(Sales[Sales Value]),
        FilteredDates
    )
```

## Interview Key Points

1. **ALL() Function is Essential** - Without it, running total won't work
2. **FILTER Logic** - Must include all dates up to current date
3. **MAX() Function** - Gets the maximum date in current context
4. **CALCULATE Context** - Modifies filter context for calculation
5. **Common Mistake** - Forgetting ALL() function leads to incorrect results

## When to Use Running Total

- **Financial Reports** - Cumulative revenue, expenses
- **Sales Analysis** - Year-to-date sales, quarterly cumulative
- **Performance Tracking** - Cumulative targets vs. actual
- **Inventory Management** - Cumulative stock levels
- **Project Management** - Cumulative costs, hours

This DAX pattern is fundamental for time intelligence calculations in Power BI and is frequently tested in interviews.