### **Time Intelligence**
5. **Year-to-Date (YTD) Sales**  
   ```DAX 
   Sales YTD = TOTALYTD([Total Sales], 'Date'[Date]) 
   ```  
   *Must know time-intelligence functions.*

6. **Prior Year Sales**  
   ```DAX 
   PY Sales = CALCULATE([Total Sales], SAMEPERIODLASTYEAR('Date'[Date])) 
   ```  
   *Key for trend analysis.*

7. **Month-over-Month Growth**  
   ```DAX 
   MoM Growth = DIVIDE([Total Sales] - [PY Sales], [PY Sales]) 
   ```  
   *Uses `DIVIDE` for safe division.*

8. **Moving Average (3 Months)**  
   ```DAX 
   3M Avg = AVERAGEX(DATESINPERIOD('Date'[Date], LASTDATE('Date'[Date]), -3, MONTH), [Total Sales]) 
   ```  
   *Combines `DATESINPERIOD` with iterators.*
