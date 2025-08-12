### **Scenario Explained:**

The video discusses a **Power BI interview question** where you need to **rank records based on multiple columns** (State and Population). Here’s the breakdown:

### **Problem:**

- A table has **3 columns**: `Customer`, `State`, and `Population`.
- There are **duplicate states** (e.g., Assam appears twice, Chennai three times, Delhi twice).
- **Goal:** Create a **measure** that ranks records **within each state** based on **population** (highest to lowest).

### **Example Data:**

|   |   |   |
|---|---|---|
|Customer|State|Population|
|Cust1|Assam|1500|
|Cust2|Assam|2500|
|Cust3|Chennai|700|
|Cust4|Chennai|4500|
|Cust5|Chennai|5000|
|Cust6|Delhi|3000|
|Cust7|Delhi|2000|

### **Expected Output:**

- For **Assam**:
    - 2500 → Rank **1**
    - 1500 → Rank **2**
- For **Chennai**:
    - 5000 → Rank **1**
    - 4500 → Rank **2**
    - 700 → Rank **3**
- For **Delhi**:
    - 3000 → Rank **1**
    - 2000 → Rank **2**

---

### **Solution: Using** `**RANKX**` **with Filtering**

Since `RANKX` alone ranks across the entire table, we need to **filter by state first** before ranking.

### **DAX Measure:**

```Plain
Rank =
VAR MaxState = MAX('Table'[State])  // Gets the current state in the row context
VAR FinalRank =
    RANKX(
        FILTER(                  // Filters the table to only the current state
            ALL('Table'),         // Ignores external filters
            'Table'[State] = MaxState
        ),
        SUM('Table'[Population]), // Ranks by population within the filtered state
        ,                        // (Skip ties parameter)
        DESC,                    // Higher population = higher rank
        DENSE                    // No gaps in ranking (optional)
    )
RETURN FinalRank
```

### **How It Works:**

1. `**MAX('Table'[State])**`
    - Captures the **current state** in the row context (e.g., "Assam" for the first two rows).
2. `**FILTER(ALL('Table'), 'Table'[State] = MaxState)**`
    - Creates a **subset of the table** containing **only rows for the current state**.
    - `ALL('Table')` ensures we ignore any external filters.
3. `**RANKX(..., SUM('Table'[Population]), DESC, DENSE)**`
    - Ranks the **filtered table** by population in **descending order**.
    - `DENSE` ensures no gaps (e.g., 1, 2, 2 → 1, 2, 3).

---

### **Key Takeaways:**

1. `**RANKX**` **alone ranks globally**—you need **filtering** to rank within groups.
2. `**FILTER + ALL**` is used to isolate records for the current state.
3. `**MAX([State])**` captures the state dynamically for each row.

This is a **common Power BI interview question** testing your ability to:

- Use **row context** in measures.
- Combine `**RANKX**` **with filtering**.
- Handle **multi-column ranking logic**.

---

### **Alternative Approach (Simpler):**

If your data model has a **relationship between tables**, you could also use:

```Plain
Rank =
RANKX(
    FILTER(ALLSELECTED('Table'), 'Table'[State] = SELECTEDVALUE('Table'[State])),
    SUM('Table'[Population]),
    ,
    DESC
)
```

Would you like a variation of this problem (e.g., ranking across multiple hierarchies)?