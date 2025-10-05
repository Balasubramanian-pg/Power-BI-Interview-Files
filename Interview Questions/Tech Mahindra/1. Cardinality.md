## What is Cardinality?

In simple terms, **cardinality refers to the number of unique values in a column.**

It's a measure of the "uniqueness" of the data within a column.

- **Low Cardinality:** A column has low cardinality if it contains very few unique values (e.g., a "Status" column with values like 'Active', 'Inactive', 'Pending').
- **High Cardinality:** A column has high cardinality if it contains many unique values, where each value appears infrequently or only once (e.g., a "Transaction ID" or "Email Address" column).

### Why Cardinality is Extremely Important in Power BI

The Power BI VertiPaq engine, which handles data in Import mode, is a **columnar database**. It achieves its amazing speed and compression by:

1. Creating a dictionary of the unique values for each column.
2. Replacing the actual values in the column with integer pointers to that dictionary.

**Low cardinality is great for this process.** The dictionary is small, and the compression is very effective.**High cardinality is bad for this process.** The dictionary becomes almost as large as the data itself, leading to poor compression, higher memory usage, and slower report performance.

### Illustration with an Example

Let's imagine a `Sales` table with 1 million rows.

|   |   |   |   |   |
|---|---|---|---|---|
|Column Name|Example Values|Cardinality (Approx.)|Type|Impact on Power BI|
|**Gender**|'Male', 'Female'|**2**|**Very Low**|**Excellent:** The engine creates a tiny dictionary. Very fast and highly compressed.|
|**Sales Region**|'North', 'South', 'East', 'West'|**4**|**Very Low**|**Excellent:** Similar to Gender. Ideal for slicers and filters.|
|**Product Category**|'Electronics', 'Clothing', 'Books'...|**50**|**Low**|**Good:** The dictionary is small. Performance is great.|
|**Customer ID**|'CUST-00001', 'CUST-00002'...|**100,000**|**High**|**Poor:** The dictionary is large. This column will use more memory and slow down relationships.|
|**Transaction ID**|'TXN-ABC-123', 'TXN-ABC-124'...|**1,000,000**|**Very High**|**Very Poor:** Every value is unique. The dictionary is as big as the column itself, offering no compression benefit.|
|**Transaction DateTime**|'2023-10-27 09:01:15', '2023-10-27 09:01:18'...|**~1,000,000**|**Very High**|**The Performance Killer:** This is often the worst offender. A DateTime column that includes seconds can be unique for every single row.|

### Practical Takeaway and Optimization Tip

A common performance issue in Power BI reports is caused by unintentionally high cardinality columns. The most frequent culprit is a **datetime** column.

**Scenario:** You have a `TransactionDateTime` column like `2023-10-27 09:01:15`. This column has very high cardinality.

**The Solution:**

In Power Query, **split this column into two separate columns:**

1. **Date Column:** e.g., `2023-10-27`. The cardinality of this column will be much lower (only 365 unique values per year).
2. **Time Column:** e.g., `09:01:15`. If you don't need to analyze by time of day, you can even remove this column entirely.

By splitting the column, you replace one very-high-cardinality column with a low-cardinality `Date` column (great for performance) and another column that is either removed or still has high cardinality but is less impactful. This single change can drastically reduce your model size and speed up your report.
