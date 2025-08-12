---
Pattern:
  - Concatenatex
---
### **Power BI Interview Solution: Displaying Slicer Selections in a Card Visual**

---

### **Problem Statement**

**Scenario:**

You need to show all selected values from a **Country slicer** in a **card visual**, formatted as a comma-separated list (e.g., "France, Germany, Italy").

---

### **DAX Measure Solution**

```Plain
Multi Select Slicer =
VAR SelectedCountries =
    CONCATENATEX(
        VALUES(Countries[Country]),  // Gets distinct selected countries
        Countries[Country],          // Column to concatenate
        ", "                         // Delimiter
    )
VAR Result =
    SWITCH(
        TRUE(),
        ISFILTERED(Countries[Country]), SelectedCountries,  // Show selections
        "All Countries"  // Default text when nothing is selected
    )
RETURN Result
```

---

### **Key Components Explained**

1. `**CONCATENATEX**`
    - Aggregates all selected values into a single string.
    - `VALUES()` ensures only distinct selections are included.
2. `**ISFILTERED**` **Check**
    - Detects if any filtering is applied to the `Country` column.
    - Returns `TRUE` when one or more countries are selected.
3. `**SWITCH(TRUE(), ...)**` **Pattern**
    - Acts like an `IF-ELSE` chain for multiple conditions.
    - Provides a fallback message ("All Countries") when no selection exists.

---

### **Implementation Steps**

1. Create a **slicer** using the `Country` column.
2. Add the **measure** to a **card visual**.
3. Test with single/multiple selections.

---

### **Advanced Enhancements**

### **1. Handle Empty Selections**

```Plain
// Improved version with empty check
Multi Select Slicer =
VAR SelectedCountries = CONCATENATEX(VALUES(Countries[Country]), Countries[Country], ", ")
VAR IsFiltered = ISFILTERED(Countries[Country])
VAR IsEmpty = COUNTROWS(VALUES(Countries[Country])) = 0
RETURN
    SWITCH(
        TRUE(),
        IsEmpty, "No countries selected",  // Special case
        IsFiltered, SelectedCountries,
        "All Countries"
    )
```

### **2. Add Count of Selections**

```Plain
// With count indicator
Multi Select Slicer =
VAR SelectedCount = COUNTROWS(VALUES(Countries[Country]))
VAR CountryList = CONCATENATEX(VALUES(Countries[Country]), Countries[Country], ", ")
RETURN
    IF(
        SelectedCount > 0,
        SelectedCount & " selected: " & CountryList,
        "All Countries"
    )
```

---

### **Why This Matters in Interviews**

✅ Demonstrates **DAX string manipulation** skills

✅ Shows understanding of **filter context** (`ISFILTERED` vs `ISCROSSFILTERED`)

✅ Provides **user-friendly UX** considerations

**Pro Tip:** Use `SELECTEDVALUE()` with a fallback for single-selection scenarios.