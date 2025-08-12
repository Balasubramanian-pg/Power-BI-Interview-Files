  

# Power BI Join Operations - Interview Question & Solution

## Question

**"Explain the different types of joins available in Power Query Editor in Power BI and demonstrate their behavior with null values. How does null handling in Power BI differ from SQL?"**

## Key Concept: Null Value Behavior in Power BI

**Critical Interview Point:** In Power BI Power Query, `null = null` evaluates to `TRUE`, which is different from SQL where `null ≠ null`.

### Proof of Null Equality

**Test Table: null_verify**

```Plain
Column A | Column B | Comparison Result
---------|----------|------------------
10       | 10       | 1 (Equal)
1        | 2        | 0 (Not Equal)
null     | null     | 1 (Equal) ← Key Point!
2        | 3        | 0 (Not Equal)
```

**Custom Column Formula:**

```Plain
if [Column A] = [Column B] then 1 else 0
```

## Sample Data Setup

**Left Table:**

```Plain
Column
------
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
Column
------
5
null
6
null
10
12
```

**Common Values:** 5, null, 10 (these will match in joins)

## Join Operations & Expected Results

### 1. Inner Join

**Definition:** Returns only matching records from both tables**Expected Output:**

```Plain
Column
------
5
5
null
null
10
10
```

_Note: Cartesian product applies - 2 records of '5' in left × 1 record of '5' in right = 2 results_

### 2. Left Outer Join

**Definition:** All records from left table + matching records from right table**Expected Output:**

```Plain
Column
------
4      ← Left table only
5
5
null
null
10
10
15     ← Left table only
```

### 3. Right Outer Join

**Definition:** All records from right table + matching records from left table**Expected Output:**

```Plain
Column
------
5
5
null
null
10
10
6      ← Right table only
12     ← Right table only
```

### 4. Left Anti Join

**Definition:** Records from left table that don't have matches in right table**Expected Output:**

```Plain
Column
------
4
15
```

### 5. Right Anti Join

**Definition:** Records from right table that don't have matches in left table**Expected Output:**

```Plain
Column
------
6
12
```

### 6. Full Outer Join

**Definition:** All records from both tables (matching + non-matching)**Expected Output:**

```Plain
Column
------
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

## Power Query M Code Example

```Plain
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

## Interview Key Points

1. **Null Behavior:** Unlike SQL, Power BI treats `null = null` as `TRUE`
2. **Join Results:** This affects all join operations, especially when null values are present
3. **Cartesian Product:** Matching records create Cartesian products (2×1=2 results)
4. **Practical Impact:** Join results in Power BI may differ significantly from equivalent SQL operations

## Why This Matters

This difference in null handling can lead to unexpected results when migrating from SQL-based solutions to Power BI, making it a crucial interview topic for Power BI developers.