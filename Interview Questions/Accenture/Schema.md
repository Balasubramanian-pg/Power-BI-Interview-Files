## **2. Star Schema vs. Snowflake Schema**  

These are two common data modeling designs used in data warehousing and BI.  

| **Feature**          | **Star Schema**                                                                 | **Snowflake Schema**                                                          |  
|-----------------------|---------------------------------------------------------------------------------|--------------------------------------------------------------------------------|  
| **Structure**         | Central **Fact table** connected directly to **Dimension tables**. Looks like a star. | Central **Fact table** connected to normalized **Dimension tables**. Looks like a snowflake. |  
| **Normalization**     | Dimensions are **denormalized** (redundant data).                               | Dimensions are **normalized** (no redundancy).                                |  
| **Query Complexity**  | Simpler and faster queries (fewer joins).                                       | More complex queries (multiple joins required).                               |  
| **Performance in Power BI** | **Highly recommended**. Optimized for Power BI’s VertiPaq engine. | **Not recommended**. Extra joins slow down performance.                       |  
| **Data Redundancy**   | Higher redundancy, slightly larger storage footprint.                          | Lower redundancy, easier maintenance.                                         |  

> [!TIP]  
> **For Power BI**, always transform a snowflake schema into a star schema in Power Query for better performance.  
