# Calculating Total YTD, QTD, or MTD in Power BI DirectQuery Mode  

## **Interview Question Context**  

**Scenario**:  
You are working with a Power BI report in **DirectQuery mode** and need to calculate:  
- **Total Year-to-Date (YTD)**  
- **Total Quarter-to-Date (QTD)**  
- **Total Month-to-Date (MTD)**  

However, Power BI’s time intelligence functions like `TOTALYTD()` **cannot be used** in DirectQuery mode.  

---

## **Approach in Import Mode (For Comparison)**  

### **DAX Measure (Import Mode)**:  
```dax  
Total YTD = TOTALYTD([Total Sales], 'Calendar'[Date])  
```  

- `TOTALYTD()` works only in Import mode.  
- Requires a date table marked as a date table.  

---

## **Challenge in DirectQuery Mode**  

**Limitation**:  
You cannot use `TOTALYTD`, `DATESYTD`, `DATESMTD`, `TOTALQTD`, etc., in DirectQuery mode.  
You must **manually simulate** the logic using `CALCULATE`, `FILTER`, and `MAX()`.  

---

## **Solution in DirectQuery Mode**  

### **DAX Measure: Total YTD**  
```dax  
Total YTD Direct =  
CALCULATE(  
    [Total Sales],  
    FILTER(  
        ALL('Calendar'),  
        'Calendar'[Year] = MAX('Calendar'[Year]) &&  
        'Calendar'[Date] <= MAX('Calendar'[Date])  
    )  
)  
```  

**Explanation**:  
- `ALL('Calendar')`: Removes row context to allow correct filtering.  
- `Year = MAX(Year)`: Limits calculation to the current year.  
- `Date <= MAX(Date)`: Includes all dates up to the selected context date.  

---

## **Solution for Total MTD**  

### **DAX Measure: Total MTD**  
```dax  
Total MTD Direct =  
CALCULATE(  
    [Total Sales],  
    FILTER(  
        ALL('Calendar'),  
        'Calendar'[Year] = MAX('Calendar'[Year]) &&  
        'Calendar'[Month] = MAX('Calendar'[Month]) &&  
        'Calendar'[Date] <= MAX('Calendar'[Date])  
    )  
)  
```  

---

## **Solution for Total QTD**  

### **DAX Measure: Total QTD**  
```dax  
Total QTD Direct =  
CALCULATE(  
    [Total Sales],  
    FILTER(  
        ALL('Calendar'),  
        'Calendar'[Year] = MAX('Calendar'[Year]) &&  
        'Calendar'[Quarter] = MAX('Calendar'[Quarter]) &&  
        'Calendar'[Date] <= MAX('Calendar'[Date])  
    )  
)  
```  

---

## **Key Insights for Interview**  

| **Topic**                  | **Explanation**                                                                 |  
|----------------------------|---------------------------------------------------------------------------------|  
| **Time Intelligence in DirectQuery** | Not supported. Must replicate manually with `CALCULATE` + `FILTER`. |  
| **Performance Consideration** | Keep the Calendar table light; DirectQuery performance depends on the SQL backend. |  
| **Calendar Table**         | Must include columns like `Year`, `Month`, `Quarter`, `Date`.                   |  
| **Measure Reusability**    | Design `[Total Sales]` as a separate base measure (`SUM(Sales[Amount])`).       |  
