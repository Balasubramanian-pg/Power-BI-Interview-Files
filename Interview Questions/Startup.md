  

---

### **Scenario-Based Power BI Interview Question (Startup Interview)**

### **Question Statement**

You are given a dataset with the following columns:

- `Product`
- `Monday Orders`
- `Tuesday Orders`
- `Wednesday Orders`

Your task is to **create a calculated column** named `**Max Orders**` that returns the **maximum number of orders placed across Monday, Tuesday, and Wednesday for each product**.

### **Example Dataset**

|   |   |   |   |
|---|---|---|---|
|Product|Monday Orders|Tuesday Orders|Wednesday Orders|
|P1|3|5|7|
|P2|5|4|2|
|P3|9|7|6|

### **Expected Output:**

|   |   |
|---|---|
|Product|Max Orders|
|P1|7|
|P2|5|
|P3|9|

---

### **Solution Using** `**SWITCH(TRUE())**` **in DAX**

### **Calculated Column Formula (DAX):**

```Plain
Max Orders =
SWITCH(
    TRUE(),
    'Table'[Monday Orders] > 'Table'[Tuesday Orders] && 'Table'[Monday Orders] > 'Table'[Wednesday Orders], 'Table'[Monday Orders],
    'Table'[Tuesday Orders] > 'Table'[Monday Orders] && 'Table'[Tuesday Orders] > 'Table'[Wednesday Orders], 'Table'[Tuesday Orders],
    'Table'[Wednesday Orders] > 'Table'[Monday Orders] && 'Table'[Wednesday Orders] > 'Table'[Tuesday Orders], 'Table'[Wednesday Orders]
)
```

---

### **Explanation of the Code:**

- `SWITCH(TRUE(), ...)` is used to evaluate multiple logical conditions and return a result as soon as one condition is met.
- The logic compares each day's order count with the other two:
    - First condition checks if `Monday Orders` is the greatest.
    - Second condition checks if `Tuesday Orders` is the greatest.
    - Third condition checks if `Wednesday Orders` is the greatest.
- As soon as a condition evaluates to `TRUE`, the corresponding value is returned.

### **Why** `**SWITCH(TRUE())**`**?**

This is a common DAX idiom to replicate `IF-ELSE IF-ELSE` behavior, where each logical expression is evaluated in sequence.

---

### **Expected Outcome in Power BI**

After applying this calculated column:

- The column `Max Orders` will be populated row-wise with the **maximum** of the three days' orders for each product.
- For the provided dataset, the output will be:
    - **P1 → 7**
    - **P2 → 5**
    - **P3 → 9**

---

If you want, I can also demonstrate the alternate solution using nested `IF()` as mentioned in the video prompt.

---

### ✅ **Optimal DAX Solution**

For this requirement, **the best and most concise** DAX solution is to use the `**MAXX()**` function over a virtual table of the three columns.

---

### **Clean and Scalable DAX Code:**

```Plain
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

---

### **Explanation:**

- `{ [Monday Orders], [Tuesday Orders], [Wednesday Orders] }`
    
    Creates a **virtual one-column table** with three rows for each row context, each containing one day's order value.
    
- `MAXX()`
    
    Iterates over this virtual table and returns the **maximum value** among those three.
    

---

### **Why This is Better:**

|   |   |   |
|---|---|---|
|Criteria|`SWITCH` / `IF` Approach|`MAXX` Approach|
|**Code Simplicity**|Verbose, repetitive|One-liner|
|**Scalability**|Hard to maintain if more days are added|Easy to scale – just add to the list|
|**Performance**|Acceptable but not elegant|Optimal for small columns|
|**Readability**|Low|High – intent is obvious|

---

### **Scalability Note:**

If you're dealing with **many day columns** or even **dynamic columns**, you should consider **unpivoting** the table and using a measure or calculated column after restructuring.

---

Let me know if you’d like to see:

- A dynamic version using `UNPIVOT` in Power Query
- A measure version instead of a calculated column

This `MAXX` approach, though, is what seasoned Power BI developers would use in a real-world model.

---

Correct — `**[Value]**` **is absolutely** _**not**_ **the product**.

Let’s clarify this precisely:

---

### 🔍 **Clarifying** `**[Value]**` **in This Context**

In this DAX expression:

```Plain
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

- You are constructing a **virtual table** (row context specific to each product row).
- That table contains **one column** with three **numeric values**:
    - The Monday order count for that product
    - The Tuesday order count for that product
    - The Wednesday order count for that product
- Since you did **not assign a column name** in the table constructor, DAX **automatically names** it `[Value]`.

---

### 📌 Example Walkthrough

Suppose you're in the row for product `"P1"`:

|   |   |   |   |
|---|---|---|---|
|Product|Monday Orders|Tuesday Orders|Wednesday Orders|
|P1|3|5|7|

In this row context, the expression:

```Plain
{ [Monday Orders], [Tuesday Orders], [Wednesday Orders] }
```

resolves to:

```Plain
{ 3, 5, 7 }
```

This becomes a virtual table like:

Value

---

3

---

5

---

7

---

Then `MAXX()` iterates over that table and returns `7`.

---

### ✅ Final Summary

- `[Value]` is **not** `Product`.
- `[Value]` refers to the **default column** created when you use a **table constructor** `{}` with scalar values.
- It **represents the order quantities**, not product names or IDs.

---

If you wanted the product, you'd refer to `[Product]`, which is in the original table — not part of the virtual table in this expression.