### **Power BI Interview Questions & Solutions (TCS)**

---

### **Question 1: Combining Text from Multiple Rows**

**Problem:**

Given a table of students and their subjects, combine all subjects for each student into a single comma-separated string.

**Input Example:**

|   |   |
|---|---|
|Name|Subject|
|John|English|
|John|Physics|
|John|Maths|

**Expected Output:**

|   |   |
|---|---|
|Name|Combined Subjects|
|John|English, Physics, Maths|

---

### **Solution (Power Query M Code)**

1. **Group By Name:**
    - Select the table → **Transform** → **Group By**
    - Group column: `Name`
    - Operation: **All Rows** (creates a nested table)
2. **Modify M Code:**
    
    ```Plain
    = Table.Group(#"Previous Step", {"Name"}, {{"Combined Subjects", each Text.Combine([Subject], ", "), type text}})
    ```
    
    - Replaces `List.Sum` (default for numbers) with `Text.Combine()`
    - `", "` adds a comma separator

**Why This Works:**

- `Text.Combine()` merges text values from lists
- Preserves original values while concatenating

---

### **Question 2: Grouping Products Without DAX**

**Problem:**

Categorize iPhone models into "Old" (11/12/13) and "New" (14/15) groups.

**Input Example:**

Product

---

iPhone 11

---

iPhone 12

---

iPhone 13

---

iPhone 14

---

iPhone 15

---

**Expected Output:**

|   |   |
|---|---|
|Category|Products|
|Old iPhones|iPhone 11, 12, 13|
|New iPhones|iPhone 14, 15|

---

### **Solution (Power BI Data Groups)**

1. **Select Products:**
    - In **Data View**, Ctrl+click to select:
        - iPhone 11, 12, 13 → Right-click → **Group**
        - iPhone 14, 15 → Right-click → **Group**
2. **Rename Groups:**
    - Change default names to "Old iPhones" and "New iPhones"

**Key Benefit:**

- No DAX required → Uses built-in **adhoc grouping**
- Groups appear in visuals automatically

---

### **Comparison of Methods**

|   |   |   |
|---|---|---|
|Task|Tool Used|Key Function|
|Text Concatenation|Power Query|`Text.Combine()`|
|Product Grouping|Power BI UI|**Data Groups** feature|

---

### **Pro Tips for Interviews**

1. For **text merging**, always prefer `Text.Combine()` over `List.Sum`.
2. Use **Data Groups** when:
    - Categories are static (e.g., product generations)
    - Avoiding DAX simplifies the solution

Need a DAX alternative for grouping? Try:

```Plain
Category =
SWITCH(
    TRUE(),
    'Table'[Product] IN {"iPhone 11","iPhone 12","iPhone 13"}, "Old iPhones",
    'Table'[Product] IN {"iPhone 14","iPhone 15"}, "New iPhones",
    "Other"
)
```