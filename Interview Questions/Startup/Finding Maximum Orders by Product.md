# Finding Maximum Orders by Product

## 1. Problem Statement
> [!NOTE]
> **Task**: Create a calculated column named `Max Orders` that returns the highest order count across Monday, Tuesday, and Wednesday for each product.

### Sample Data
| Product | Monday Orders | Tuesday Orders | Wednesday Orders |
|---------|--------------|---------------|-----------------|
| P1      | 3            | 5             | 7               |
| P2      | 5            | 4             | 2               |

### Expected Output
| Product | Max Orders |
|---------|-----------|
| P1      | 7         |
| P2      | 5         |

## 2. Recommended Solution: MAXX() Approach

```dax
Max Orders =
MAXX(
    {
        [Monday Orders],
        [Tuesday Orders],
        [Wednesday Orders]
    },
    [Value]
)
```

### How It Works
1. **Virtual Table Creation**: 
   - `{ [Monday Orders], [Tuesday Orders], [Wednesday Orders] }` creates an in-memory table with one column (automatically named `[Value]`)
   
2. **MAXX Function**: 
   - Scans all values in the virtual table
   - Returns the maximum value for each row

> [!TIP]
> **Advantages**:
> - Single-line, scalable code
> - Automatically adapts if new day columns are added
> - Better performance than nested IF/SWITCH

## 3. Alternative Solution: SWITCH(TRUE())

```dax
Max Orders =
SWITCH(
    TRUE(),
    [Monday Orders] >= [Tuesday Orders] && [Monday Orders] >= [Wednesday Orders], [Monday Orders],
    [Tuesday Orders] >= [Wednesday Orders], [Tuesday Orders],
    [Wednesday Orders]
)
```

> [!CAUTION]
> **Limitations**:
> - Verbose code (harder to maintain)
> - Requires manual updates for new columns
> - More prone to logical errors

## 4. Key Comparison

| Criteria        | MAXX Approach | SWITCH Approach |
|----------------|--------------|----------------|
| Code Length    | 1 line       | 5+ lines       |
| Scalability    | ✅ Easy       | ❌ Manual updates |
| Readability    | High         | Medium         |
| Performance    | Optimal      | Acceptable     |

> [!IMPORTANT]
> **Best Practice**: Always prefer MAXX() for this scenario unless you need specific conditional logic.

## 5. Advanced Options

### Power Query Alternative (Unpivot)
```m
= Table.AddColumn(
    Source, 
    "Max Orders", 
    each List.Max({
        [Monday Orders], 
        [Tuesday Orders], 
        [Wednesday Orders]
    })
)
```

### Dynamic Column Handling
```dax
// For scenarios with variable day columns
Max Orders = 
MAXX(
    SELECTCOLUMNS(
        FILTER(
            ADDCOLUMNS(
                SUMMARIZE('Table', [Product]),
                "DayValue", [Monday Orders] // Repeat for other days
            ),
            [DayValue] <> BLANK()
        ),
        "Value", [DayValue]
    ),
    [Value]
)
```

> [!WARNING]
> Reserve dynamic solutions for complex scenarios with frequently changing columns.

## 6. Verification Checklist
1. [ ] Test with tie values (e.g., Monday=5, Tuesday=5)
2. [ ] Verify with blank/null values 
3. [ ] Check performance with large datasets (>100K rows)
4. [ ] Validate against manual calculations for sample data
