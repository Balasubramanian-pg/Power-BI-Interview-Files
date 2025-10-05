## 9. **Percentage of Total Sales**  
   ```DAX 
   % Total = DIVIDE([Total Sales], CALCULATE([Total Sales], ALL(Sales))) 
   ```  
   *Uses `ALL` to remove filters.*
