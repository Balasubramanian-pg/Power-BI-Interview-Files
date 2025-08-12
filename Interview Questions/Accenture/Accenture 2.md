## **Question 1: Why This DAX Code Fails**

### **Scenario**

You are given a measure that tries to count rows from a `Sales` table **where total sales (a measure) > 500** using this incorrect syntax:

```Plain
Measure = CALCULATE(COUNTROWS(Sales), [Total Sales] > 500)
```

### **Error**

This results in a **semantic error**:

> "A function placeholder has been used in a true/false expression that is used as a table filter expression. This is not allowed."

### **Root Cause**

- In DAX, **you cannot directly use a measure** like `[Total Sales] > 500` inside the filter argument of `CALCULATE()`.
- The filter argument must evaluate to a table, not a scalar boolean.

---

### **Correct Implementation**

Use the `FILTER()` function to iterate over a table and apply a row-level logical condition.

```Plain
CorrectMeasure =
CALCULATE(
    COUNTROWS(Sales),
    FILTER(
        Sales,
        [Total Sales] > 500
    )
)
```

### **Explanation**

- `FILTER(Sales, [Total Sales] > 500)` returns a **filtered table** where only rows with total sales > 500 remain.
- `CALCULATE` changes the evaluation context and counts rows on that filtered table.

---

### **Practical Insight**

This question tests understanding of **context transition** and how measures behave inside row context. A rookie mistake is to treat measures as direct filters — which they are not. This is a textbook debugging scenario in real-world BI development.

---

## **Question 2: DAX Optimization Using** `**SELECTEDVALUE()**`

### **Initial DAX Pattern**

```Plain
Measure =
IF(
    HASONEVALUE(Sales[Country]),
    VALUES(Sales[Country])
)
```

### **Purpose**

To return the selected country name from a slicer.

### **Problem**

While functional, it is **inefficient**, using:

- `HASONEVALUE()` to ensure a single selection.
- `VALUES()` to retrieve that value.

This is verbose and can impact performance on large models.

---

### **Optimized Approach Using** `**SELECTEDVALUE()**`

```Plain
OptimizedMeasure = SELECTEDVALUE(Sales[Country])
```

### **Benefits**

- **Cleaner** syntax.
- Internally optimized by the engine.
- Eliminates redundant conditional checks.
- Results in **reduced DAX query plan time**, as seen in **Performance Analyzer**.

---

### **Practical Insight**

This tests awareness of **modern, idiomatic DAX**. `SELECTEDVALUE()` is a **performance-aware syntactic sugar** for a common IF + VALUES construct. Optimizing for simplicity without sacrificing accuracy is critical in production-grade dashboards.

---

## **Key Takeaways for Interview Readiness**

|   |   |   |
|---|---|---|
|Aspect|Question 1|Question 2|
|DAX Functionality Tested|Context transition, CALCULATE filters|Slicer value extraction|
|Common Mistake|Direct measure in filter expression|Verbose logic when a shortcut exists|
|Solution|Wrap filter logic in `FILTER()`|Use `SELECTEDVALUE()` instead|
|Interview Edge|Shows debugging skills|Shows DAX fluency and performance focus|

---
