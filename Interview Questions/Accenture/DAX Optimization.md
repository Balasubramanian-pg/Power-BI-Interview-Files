## **Question 1: Why This DAX Code Fails**  

### **Scenario**  
You are given a measure that attempts to count rows from a `Sales` table where the total sales (a measure) exceed 500, using the following incorrect syntax:  

```dax
Measure = CALCULATE(COUNTROWS(Sales), [Total Sales] > 500)
```

### **Error**  
This results in a semantic error:  
```  
"A function placeholder has been used in a true/false expression that is used as a table filter expression. This is not allowed."  
```

### **Root Cause**  
In DAX, you cannot directly use a measure like `[Total Sales] > 500` inside the filter argument of `CALCULATE()`. The filter argument must evaluate to a **table**, not a scalar boolean.  

### **Correct Implementation**  
Use the `FILTER()` function to iterate over a table and apply a row-level logical condition.  

```dax
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
- `FILTER(Sales, [Total Sales] > 500)` returns a filtered table where only rows with total sales > 500 remain.  
- `CALCULATE` changes the evaluation context and counts rows on that filtered table.  

> [!IMPORTANT]  
> Always use `FILTER()` when applying row-level conditions in `CALCULATE()`.  

### **Practical Insight**  
This question tests understanding of **context transition** and how measures behave inside row context. A common rookie mistake is treating measures as direct filters—which they are not. This is a classic debugging scenario in real-world BI development.  

---

## **Question 2: DAX Optimization Using `SELECTEDVALUE()`**  

### **Initial DAX Pattern**  
```dax
Measure = 
IF(
    HASONEVALUE(Sales[Country]),
    VALUES(Sales[Country])
)
```

### **Purpose**  
To return the selected country name from a slicer.  

### **Problem**  
While functional, this approach is inefficient, as it uses:  
- `HASONEVALUE()` to ensure a single selection.  
- `VALUES()` to retrieve that value.  

This is verbose and can impact performance on large models.  

### **Optimized Approach Using `SELECTEDVALUE()`**  
```dax
OptimizedMeasure = SELECTEDVALUE(Sales[Country])
```

### **Benefits**  
- **Cleaner syntax.**  
- **Internally optimized** by the engine.  
- **Eliminates redundant conditional checks.**  
- **Reduces DAX query plan time**, as observed in Performance Analyzer.  

> [!TIP]  
> `SELECTEDVALUE()` is a performance-aware syntactic sugar for a common `IF + VALUES` construct.  

### **Practical Insight**  
This tests awareness of modern, idiomatic DAX. Optimizing for simplicity without sacrificing accuracy is critical in production-grade dashboards.  

---

## **Key Takeaways for Interview Readiness**  

| **Aspect**                  | **Question 1**                          | **Question 2**                          |  
|-----------------------------|-----------------------------------------|-----------------------------------------|  
| **DAX Functionality Tested** | Context transition, `CALCULATE` filters | Slicer value extraction                 |  
| **Common Mistake**          | Direct measure in filter expression     | Verbose logic when a shortcut exists    |  
| **Solution**                | Wrap filter logic in `FILTER()`         | Use `SELECTEDVALUE()` instead           |  
| **Interview Edge**          | Shows debugging skills                  | Shows DAX fluency and performance focus |  

---

This document highlights common pitfalls and best practices in DAX, emphasizing both debugging techniques and optimization strategies. It’s designed to prepare candidates for technical interviews focusing on DAX and Power BI development.  

> [!TIP]  
> Practice these patterns with real-world datasets to reinforce your understanding of DAX optimization and error handling.  

---
