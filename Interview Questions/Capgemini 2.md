### **Power BI Interview Solution: Calculating Female Sales in Specific Indian States**

---

### **Problem Statement**

**Task:**

Create a DAX measure to calculate total sales from female customers in **Karnataka, Maharashtra, and Gujarat** for a WMart India dataset.

**Data Structure:**

|   |   |
|---|---|
|Column|Type|
|Customer|Text|
|Gender|Text (M/F)|
|City|Text|
|State|Text|
|Amount|Currency|

---

### **Optimal DAX Solution**

### **1. Final Measure Code**

```Plain
Female Sales Selected States =
CALCULATE(
    SUM(Sales[Amount]),
    Sales[Gender] = "F",
    Sales[State] IN {"Karnataka", "Maharashtra", "Gujarat"}
)
```

### **2. Key Components Explained**

|   |   |   |
|---|---|---|
|Function/Component|Purpose|Why It's Used|
|`CALCULATE()`|Modifies filter context|Core function for conditional sums|
|`SUM(Sales[Amount])`|Aggregates sales values|Base calculation|
|`Sales[Gender] = "F"`|Filter condition|Isolates female customers|
|`IN {}` operator|Multi-value comparison|Cleaner than multiple OR conditions|

### **3. Alternative Implementations**

**Option A (Using FILTER)**

```Plain
Female Sales Selected States =
CALCULATE(
    SUM(Sales[Amount]),
    FILTER(
        Sales,
        Sales[Gender] = "F" &&
        Sales[State] IN {"Karnataka", "Maharashtra", "Gujarat"}
    )
)
```

_Best for complex filtering logic_

**Option B (Using Variables)**

```Plain
Female Sales Selected States =
VAR TargetStates = {"Karnataka", "Maharashtra", "Gujarat"}
RETURN
    CALCULATE(
        SUM(Sales[Amount]),
        Sales[Gender] = "F",
        Sales[State] IN TargetStates
    )
```

_Improves readability and reusability_

---

### **Performance Considerations**

1. **Filter Order Matters**
    - Apply gender filter first (typically higher selectivity)
    - Then state filter
2. **Avoid Nested FILTERs**
    
    The simplified `CALCULATE` with direct filters is more efficient than wrapping in `FILTER()` for simple conditions.
    
3. **Consider Data Model**
    - If State/Gender are in dimension tables, use relationships instead
    - Implement row-level security if needed

---

### **Expected Output**

For the sample data:

- Total Female Sales (All States): ₹3,500
- Female Sales (Target States): ₹2,100_(Assuming 60% of female sales come from the 3 target states)_

---

### **Why This Solution Works for Interviews**

✅ **Demonstrates Core DAX Skills**

- `CALCULATE` mastery
- Filter context manipulation

✅ **Shows Optimization Awareness**

- Clean syntax vs. verbose alternatives
- Performance considerations

✅ **Handles Real-World Requirements**

- Geographic segmentation
- Demographic filtering

**Pro Tip:** Always verify your measure with a pivot table before declaring completion!