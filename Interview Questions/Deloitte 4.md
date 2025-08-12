## ✅ **Scenario Summary**

**Objective:**

Create a **calculated table** using DAX that includes only 3 specific columns from a larger table and satisfies **3 filter conditions**.

---

## 📄 **Problem Statement**

You’re given a table with multiple columns (Product ID, Product Name, Price, Color, Quantity, etc.).

### ❓You are asked to:

> Create a new table with only three columns:
> 
> 1. Product ID
> 2. Product Name
> 3. Product Price

And apply the following filters:

- `Product ID <= 100`
- `Product Name` starts with `"S"` (case-insensitive)
- `Price >= 500`

---

## 🧠 **Concepts Tested**

- Creating calculated tables using DAX
- Use of `CALCULATETABLE`
- Filtering with `SEARCH()`
- Column selection with `SUMMARIZECOLUMNS`

---

## 🛠️ **DAX Solution**

```GraphQL
Required Table =
CALCULATETABLE(
    SUMMARIZECOLUMNS(
        Products[Product ID],
        Products[Product Name],
        Products[Price]
    ),
    Products[Product ID] <= 100,
    Products[Price] >= 500,
    SEARCH("s", Products[Product Name], 1, BLANK()) = 1
)
```

---

## 🧾 **Explanation**

### 1. `CALCULATETABLE`

- Used to **create a new table** filtered based on specific conditions.
- Accepts a **base table** and **filter expressions**.

### 2. `SUMMARIZECOLUMNS`

- Extracts only the required **three columns** from the original table:
    - `Product ID`
    - `Product Name`
    - `Price`

### 3. `Filter Conditions`

- `Products[Product ID] <= 100`: Straightforward numeric filter
- `Products[Price] >= 500`: Straightforward numeric filter
- `SEARCH("s", Products[Product Name], 1, BLANK()) = 1`:
    - Searches for the letter "s" at the **first character** (position 1) of the `Product Name`
    - Case-insensitive, unlike `FIND()` which is case-sensitive
    - Returns rows where the name **starts** with "s" or "S"

---

## 📊 **Expected Output**

A new table with:

|   |   |   |
|---|---|---|
|Product ID|Product Name|Price|
|84|Shoes|520|
|99|Shampoo|750|
|100|Speakers|1200|

(Example only - actual values depend on source data)

---

## 💡 **Best Practice Notes**

- **Use** `**SEARCH()**` **over** `**FIND()**` for case-insensitive checks.
- `**CALCULATETABLE()**` **+** `**SUMMARIZECOLUMNS()**` is a clean way to filter + project required columns in one go.
- Avoid `SELECTCOLUMNS()` here as it doesn’t natively support complex filters like `CALCULATETABLE()` does.

---

## ✅ **Alternate Solution #1: Using** `**SELECTCOLUMNS**` **+** `**FILTER**`

This version is more readable and modular, especially for row-based filtering.

### 🔧 **DAX Code:**

```Plain
Required Table =
FILTER(
    SELECTCOLUMNS(
        Products,
        "Product ID", Products[Product ID],
        "Product Name", Products[Product Name],
        "Price", Products[Price]
    ),
    Products[Product ID] <= 100 &&
    Products[Price] >= 500 &&
    SEARCH("s", Products[Product Name], 1, BLANK()) = 1
)
```

---

### 🧾 **Explanation:**

- `SELECTCOLUMNS` builds a virtual table with only 3 columns.
- `FILTER` applies row-level conditions:
    - ID ≤ 100
    - Price ≥ 500
    - Name starts with `"s"` (case-insensitive)

This method is ideal when you **only need specific columns** and **don’t want to project all columns** from the original table.

---

## ✅ **Alternate Solution #2: With** `**ADDCOLUMNS**` **for Extra Logic**

If you need to compute additional fields or flags while keeping filters clean:

```Plain
Required Table =
FILTER(
    ADDCOLUMNS(
        Products,
        "StartsWithS", SEARCH("s", Products[Product Name], 1, BLANK())
    ),
    Products[Product ID] <= 100 &&
    Products[Price] >= 500 &&
    [StartsWithS] = 1
)
```

Then use `SELECTCOLUMNS` on this result if you want to keep only the 3 columns:

```Plain
SELECTCOLUMNS(
    Required Table,
    "Product ID", Products[Product ID],
    "Product Name", Products[Product Name],
    "Price", Products[Price]
)
```

---

## 🧠 **When to Use Which:**

|   |   |
|---|---|
|Method|Use Case|
|`CALCULATETABLE` + `SUMMARIZECOLUMNS`|Best for grouped or aggregated results|
|`FILTER` + `SELECTCOLUMNS`|Best for clean row-level filters without grouping|
|`ADDCOLUMNS` + `FILTER`|Best when you want to **precompute flags or metrics**|

---

Would you like me to demonstrate how this can be tied to a **date slicer** or **parameter table** for dynamic filtering during user interaction?