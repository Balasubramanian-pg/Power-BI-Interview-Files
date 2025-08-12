## 1. Retrieving Previous Row Values in DAX

### 1.1 Problem Statement
> [!NOTE]
> **Task**: Create a column showing the previous row's Transaction Date for each record.

### 1.2 Sample Data
| ID | Transaction Date |
|----|------------------|
| 1  | 2024-08-01       |
| 2  | 2024-08-03       |
| 3  | 2024-08-05       |

### 1.3 Optimal Solution (MAXX + FILTER + EARLIER)
```dax
Previous Date = 
MAXX(
    FILTER(
        'Data',
        'Data'[ID] = EARLIER('Data'[ID]) - 1
    ),
    'Data'[Transaction Date]
)
```

### 1.4 How It Works
1. `EARLIER` maintains current row context
2. `FILTER` finds row with ID = Current ID - 1
3. `MAXX` extracts the single value

> [!TIP]
> For time-series data without sequential IDs, use:
> ```dax
> FILTER('Data', 'Data'[Date] < EARLIER('Data'[Date]))
> ```

## 2. MAXX vs MINX in DAX

### 2.1 Key Difference
| Function | Returns | Use Case |
|----------|---------|----------|
| MAXX | Maximum value | Latest transaction |
| MINX | Minimum value | First transaction |

### 2.2 When They Differ
```dax
// Returns most recent prior date
Last Prior Date = 
MAXX(
    FILTER(Sales, 
        Sales[CustomerID] = EARLIER(Sales[CustomerID]) &&
        Sales[Date] < EARLIER(Sales[Date])
    ,
    Sales[Date]
)

// Returns earliest prior date  
First Prior Date =
MINX(
    FILTER(Sales,
        Sales[CustomerID] = EARLIER(Sales[CustomerID]) &&
        Sales[Date] < EARLIER(Sales[Date]))
    ,
    Sales[Date]
)
```

## 3. Scalar vs Vector Values

### 3.1 Comparison
| Characteristic | Scalar | Vector |
|---------------|--------|--------|
| Dimensionality | Single value | Column/table |
| DAX Context | Measures | Iterators |
| Example | `SUM([Sales])` | `Sales[Product]` |

### 3.2 Practical Examples
```dax
// Scalar (measure)
Total Profit = [Revenue] - [Cost]

// Vector (iterator)
Avg Sales = AVERAGEX(VALUES(Products[ID]), [Sales])
```

> [!WARNING]
> Common mistake: Using FILTER directly in SUM
> ```dax
> // Wrong:
> SUM(FILTER(Sales, Sales[Qty] > 100))
> 
> // Correct:
> SUMX(FILTER(Sales, Sales[Qty] > 100), Sales[Amount])
> ```

## 4. Advanced Techniques

### 4.1 Dynamic Previous Value Calculation
```dax
Previous Dynamic = 
VAR CurrentDate = 'Data'[Date]
RETURN
MAXX(
    FILTER(ALL('Data'),
        'Data'[Date] < CurrentDate &&
        'Data'[CustomerID] = EARLIER('Data'[CustomerID])
    ,
    'Data'[Date]
)
```

### 4.2 Handling Ties
```dax
// With MAXX
Latest Value = 
MAXX(
    FILTER('Data',
        'Data'[Date] = MAXX(
            FILTER(ALL('Data'),
                'Data'[CustomerID] = EARLIER('Data'[CustomerID]) &&
                'Data'[Date] < EARLIER('Data'[Date])
            ),
            'Data'[Date]
        )
    ),
    'Data'[Value]
)
```

## Key Takeaways

1. **Row Context**: Master EARLIER for nested row operations
2. **Performance**: MAXX/MINX outperform nested IF/SWITCH
3. **Data Types**: Distinguish scalar vs vector for proper DAX usage
4. **Dynamic Solutions**: Adapt formulas for real-world data variability

> [!IMPORTANT]
> Always validate with edge cases:
> - First/last records
> - Tied values
> - Blank/missing data
