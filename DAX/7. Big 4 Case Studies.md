## **Financial Reporting & Compliance**
1. **EBITDA (Earnings Before Interest, Taxes, Depreciation, Amortization)**  
   ```DAX
   EBITDA = 
   [Total Revenue] - [Operating Expenses] + [Depreciation] + [Amortization]
   ```  
   *Critical for financial statements. Be ready to explain how you’d source the data.*

2. **Accrual vs. Cash Basis Reporting**  
   ```DAX
   Accrual Revenue = 
   SUM(Revenue[Amount])  // Recognized when earned
   Cash Revenue = 
   CALCULATE(SUM(Revenue[Amount]), Payment[Status] = "Paid")
   ```  
   *Big 4 often work with accrual accounting adjustments. Highlight the difference.*

---

### **Client Analytics & KPI Tracking**
3. **Customer Churn Rate**  
   ```DAX
   Churn Rate = 
   DIVIDE(
     COUNTROWS(FILTER(Customer, Customer[Status] = "Inactive")),
     COUNTROWS(Customer)
   )
   ```  
   *Key for SaaS/Subscription clients. Mention cohort analysis for retention.*

4. **Net Promoter Score (NPS) Segmentation**  
   ```DAX
   NPS Category = 
   SWITCH(
     TRUE(),
     [NPS Score] >= 9, "Promoter",
     [NPS Score] <= 6, "Detractor",
     "Passive"
   )
   ```  
   *Shows client experience focus. Link NPS to revenue growth in interviews.*

---

### **Budgeting & Forecasting**
5. **Forecast Accuracy (MAPE)**  
   ```DAX
   MAPE = 
   AVERAGEX(
     Sales,
     DIVIDE(ABS([Actual] - [Forecast]), [Actual])
   )
   ```  
   *Measures demand planning accuracy. Explain why MAPE matters for FP&A.*

6. **Variance Analysis with Waterfall Logic**  
   ```DAX
   Budget Waterfall = 
   VAR Plan = [Budget]
   VAR Actual = [Total Sales]
   RETURN
   SWITCH(
     SELECTEDVALUE(Dimension[Breakdown]),
     "Volume", (Actual - Plan) * [Average Price],
     "Price", (Actual - Plan) * [Volume]
   )
   ```  
   *Break down drivers of variance (volume vs. price).*

---

### **Data Governance & Quality**
7. **Duplicate Records Check**  
   ```DAX
   Duplicates = 
   COUNTROWS(Sales) - COUNTROWS(SUMMARIZE(Sales, Sales[InvoiceID]))
   ```  
   *Data quality is huge in audits. Highlight how you’d clean this.*

8. **Missing Data Flags**  
   ```DAX
   % Missing Data = 
   DIVIDE(
     COUNTROWS(FILTER(Sales, ISBLANK(Sales[CustomerID]))),
     COUNTROWS(Sales)
   )
   ```  
   *Demonstrate rigor in data validation.*

---

### **Industry-Specific Scenarios**
9. **Retail: Sell-Through Rate**  
   ```DAX
   Sell-Through Rate = 
   DIVIDE(SUM(Inventory[Sold]), SUM(Inventory[Received]))
   ```  
   *Common in retail/CPG. Explain inventory turnover implications.*

10. **Banking: Loan-to-Value (LTV) Ratio**  
    ```DAX
    LTV = 
    DIVIDE(SUM(Loans[Balance]), SUM(Property[AppraisedValue]))
    ```  
    *Critical for risk assessment. Mention regulatory compliance (e.g., Basel III).*

---

### **Advanced-But-Practical DAX**
11. **Dynamic Top N with Parameter Table**  
    ```DAX
    Top N Sales = 
    CALCULATE(
      [Total Sales],
      TOPN(SELECTEDVALUE(Parameter[N]), ALL(Product), [Total Sales])
    )
    ```  
    *Clients love interactive reports. Use a parameter table for flexibility.*

12. **Time-Weighted Average (e.g., Average Balance)**  
    ```DAX
    Avg Daily Balance = 
    SUMX(
      VALUES('Date'[Date]),
      [Daily Balance] * 'Date'[DaysInMonth]
    ) / SUM('Date'[DaysInMonth])
    ```  
    *Used in financial services for interest calculations.*

---

### **Audit & Reconciliation**
13. **General Ledger Reconciliation**  
    ```DAX
    Reconciliation Gap = 
    ABS([Subledger Total] - [GL Total])
    ```  
    *Auditors reconcile balances. Explain how you’d investigate gaps.*

14. **Threshold-Based Alerts**  
    ```DAX
    Fraud Alert = 
    IF(
      [Transaction Amount] > 10000, 
      "Review Needed", 
      "Approved"
    )
    ```  
    *Anti-money laundering (AML) use case. Mention automation in audits.*

---

### **Consulting Soft Skills in DAX**
15. **Simplifying Complex Logic for Stakeholders**  
    ```DAX
    Sales Growth (Simplified) = 
    "Growth: " & FORMAT([YoY Growth], "0%")
    ```  
    *Use `FORMAT` to make metrics user-friendly. Big 4 values communication!*

16. **Benchmarking vs. Industry Peers**  
    ```DAX
    Performance vs. Peer Avg = 
    DIVIDE([Client Sales], [Industry Average Sales])
    ```  
    *Contextualize results for client strategy sessions.*

---

### **Case Study Prep**
17. **Customer Lifetime Value (CLV) with Discount Rate**  
    ```DAX
    CLV = 
    SUMX(
      Customer,
      [Annual Profit] / (1 + [Discount Rate])^Customer[Tenure]
    )
    ```  
    *Showcases NPV understanding for valuation cases.*

18. **Breakeven Analysis**  
    ```DAX
    Breakeven Units = 
    DIVIDE([Fixed Costs], [Price per Unit] - [Variable Cost per Unit])
    ```  
    *Classic case study question. Link DAX to business strategy.*

---

### **Bonus: Behavioral + Technical Mix**
19. **Explain a Time You Optimized a Slow Report**  
    *Example Answer:*  
    “I reduced a client’s report load time by 70% using **SUMMARIZECOLUMNS** to pre-aggregate data and **variables** to avoid redundant calculations. I also removed nested `FILTER` calls.”  

20. **How Would You Validate a Critical Measure?**  
    *Example Answer:*  
    “I’d cross-check DAX results with source SQL queries, test edge cases (e.g., division by zero), and validate with business users to ensure alignment with operational definitions.”

---

### **Big 4 Interview Strategy**
1. **Focus on Business Impact**: Always tie DAX to outcomes:  
   - *“This measure helped a client reduce inventory costs by 15%.”*  
   - *“We automated variance reporting, saving 10 hours/week.”*  
2. **Use STAR Framework**:  
   - **Situation**: “A retail client had inconsistent sales metrics.”  
   - **Task**: “I was tasked with standardizing YoY growth calculations.”  
   - **Action**: “Built a DAX measure using `SAMEPERIODLASTYEAR` and error handling.”  
   - **Result**: “Stakeholders could trust the reports, leading to faster decisions.”  
3. **Practice Whiteboarding**: Sketch a simple data model (e.g., Sales, Date, Product) and walk through filter context.  
4. **Ask Clarifying Questions**:  
   - *“Should this metric include returns?”*  
   - *“Is the data daily or monthly granularity?”*

---

This list ensures you’re ready for **90% of Big 4 technical + behavioral questions**. Pair it with mock interviews (practice explaining measures aloud!) and a few case studies to build confidence. Good luck! 🚀
