# **Generating an Index Based on Combinations of Two Columns Using `EARLIER()` in Power BI**  
## **INTERVIEW QUESTION**  

> **Scenario**:  
> You are given a dataset with two columns — `ApplePhone` and `Supplier`. You must create an **index column** such that:  
> - The index **restarts** for every new `ApplePhone` value.  
> - Within each phone group, the index should increment **based on alphabetical order of** `Supplier`.  

### **Example**  

| ApplePhone | Supplier | Index |  
|------------|----------|------|  
| iPhone 13  | India    | 1    |  
| iPhone 13  | Mexico   | 2    |  
| iPhone 13  | Spain    | 3    |  
| iPhone 14  | India    | 1    |  
| iPhone 14  | Mexico   | 2    |  
| iPhone 14  | Spain    | 3    |  
| iPhone 14  | USA      | 4    |  

> [!NOTE]  
> This problem tests your ability to handle row context and use the `EARLIER()` function in DAX.  

## **SOLUTION: Use `EARLIER()` Function in a Calculated Column**  

### **🔧 DAX Formula**  

```dax  
Index =  
CALCULATE(  
    COUNTROWS('Table'),  
    FILTER(  
        'Table',  
        'Table'[ApplePhone] = EARLIER('Table'[ApplePhone]) &&  
        'Table'[Supplier] <= EARLIER('Table'[Supplier])  
    )  
)  
```  

## **Explanation**  

| **DAX Component** | **Purpose**                                                                 |  
|--------------------|-----------------------------------------------------------------------------|  
| `CALCULATE()`      | Evaluates an expression in a modified filter context.                       |  
| `COUNTROWS()`      | Counts the number of rows that meet the condition.                          |  
| `FILTER('Table', ...)` | Creates a row context for counting within the same phone group.           |  
| `EARLIER()`        | Refers to the current row context for the evaluated `ApplePhone` and `Supplier`. |  

> [!TIP]  
> `EARLIER()` is crucial for comparing the current row with other rows in the same table during iteration.  

## **How the Index Works**  

For each row:  
- It **counts** how many rows have:  
  - The same `ApplePhone`, and  
  - A `Supplier` value **less than or equal to** the current row’s supplier (alphabetically).  

**Example**:  
- For `iPhone 13` group:  
  - `India` = 1  
  - `Mexico` = 2  
  - `Spain` = 3  
- For `iPhone 14` group:  
  - Same logic applies, restarting the count at 1.  

## **When to Use `EARLIER()`**  

- When building **row-level calculations** that depend on a comparison between:  
  - **Current row context**, and  
  - **Evaluated context inside** `FILTER()`.  

> [!IMPORTANT]  
> This is a **classic Power BI interview question** that tests your grasp of row context and nested evaluations.

This document provides a clear, step-by-step explanation of generating an index based on combinations of two columns using `EARLIER()` in Power BI, addressing common interview questions and challenges.  

> [!TIP]  
> Practice this pattern with different datasets to reinforce your understanding of row context and `EARLIER()` in DAX.  
