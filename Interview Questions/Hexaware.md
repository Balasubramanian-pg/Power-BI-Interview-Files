---
Pattern:
  - Switch
---
### **Power BI Interview Solution: Dynamic Number Formatting with DAX**

---

### **Problem Statement**

**Given:**

- A table with three columns:
    - **Description** (e.g., "value one", "percentage")
    - **Value** (numeric values like 12789.00, 99.00)
    - **Type** (specifies "decimal" or "percentage")

**Task:**

Create a calculated column that formats the Value column differently based on the Type:

- **Decimal**: Comma-separated thousands (12,789)
- **Percentage**: One decimal place with % symbol (99.0%)

---

### **DAX Solution**

```Plain
Formatted Result =
SWITCH(
    [Type],
    "decimal", FORMAT([Value], "#,##0"),
    "percentage", FORMAT([Value]/100, "0.0%"),
    [Value]  // Default fallback
)
```

---

### **Key Components Explained**

1. `**SWITCH**` **Function**
    - Evaluates the [Type] column
    - Acts as an IF-THEN-ELSE chain for multiple conditions
2. `**FORMAT**` **Function Patterns**
    - `#,##0` for decimals:
        - `#` = optional digit
        - `,` = thousands separator
        - `0` = required digit
    - `0.0%` for percentages:
        - Divides by 100 to convert decimal to percentage
        - `0.0` = one decimal place
        - `%` = percentage symbol
3. **Error Handling**
    - Default fallback returns original value if Type is unexpected

---

### **Alternative Implementation**

For more complex scenarios:

```Plain
Formatted Result =
VAR BaseValue = [Value]
VAR FormatType = [Type]
RETURN
    IF(
        FormatType = "percentage",
        FORMAT(BaseValue/100, "0.0%"),
        FORMAT(BaseValue, "#,##0")
    )
```

**Advantages:**

- Uses variables for readability
- Easier to extend with additional formats

---

### **Visual Demonstration**

|   |   |   |   |
|---|---|---|---|
|Description|Value|Type|Formatted Result|
|Revenue|12789.00|decimal|12,789|
|Growth Rate|99.00|percentage|99.0%|
|Target|98.00|percentage|98.0%|

---

### **Interview Insights**

✅ **Tests Knowledge Of:**

- DAX text formatting functions
- Conditional logic implementation
- Data type conversion awareness

⚠️ **Common Mistakes:**

- Forgetting to divide percentages by 100
- Using incorrect format strings (e.g., "#.##" vs "#,##0")
- Not handling null/blank values

---

### **Pro Tips**

1. **For Measures:**
    
    Use the same logic but wrap with `SELECTEDVALUE()` for dynamic formatting in visuals.
    
2. **Localization:**
    
    Replace `#,##0` with `#.##0` for European formats.
    
3. **Performance:**
    
    Calculated columns increase model size - consider Power Query transformations for static data.