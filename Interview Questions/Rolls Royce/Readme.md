# Comprehensive Study Notes: Row-Level Security (RLS) in Power BI

## Introduction to Row-Level Security (RLS)

### What is RLS?
* **Row-Level Security (RLS)** is a security feature in Power BI that restricts data access for specific users at the row level
* RLS filters data based on roles assigned to users, ensuring they only see data they're authorized to access
* Applied at the dataset level and enforced regardless of how users access the report (web, mobile, embedded)
* Works by filtering tables based on DAX expressions that evaluate user identity or predefined values

### Why Use RLS?
* **Data Security**: Prevents unauthorized access to sensitive information
* **Multi-tenant Reporting**: Single report serves multiple clients/departments with filtered data
* **Compliance**: Helps meet regulatory requirements for data privacy and access control
* **Scalability**: Eliminates need for creating separate reports for different user groups
* **Centralized Management**: Control data access from one location rather than managing multiple reports

> [!IMPORTANT]
> RLS must be configured in Power BI Desktop before publishing to Power BI Service. Once published, roles can be managed and users assigned in the service.

---

## Types of RLS in Power BI

### 1. Static RLS
* **Definition**: Fixed, predefined filters applied to specific roles
* Users assigned to a role always see the same filtered dataset
* Filter criteria are hardcoded in the role definition
* Requires creating separate roles for each filter combination

**Characteristics:**
* Simple to implement and understand
* Filter values are explicitly defined (e.g., Country = "France")
* No dynamic evaluation based on user identity
* Best for scenarios with limited, stable user groups

> [!TIP]
> Static RLS is ideal when you have a small number of user groups with clearly defined data access requirements.

### 2. Dynamic RLS
* **Definition**: Filters data based on the logged-in user's identity
* Uses DAX functions to automatically determine what data users can access
* Requires a security table mapping users to their allowed data
* Single role can serve multiple users with different data access

**Characteristics:**
* Scalable solution for large user bases
* Filter criteria evaluated at runtime based on user context
* Uses `USERPRINCIPALNAME()` function to identify logged-in user
* Requires proper data model design with security tables

> [!IMPORTANT]
> Dynamic RLS requires a security table that maps user email addresses to their permitted data values (countries, products, departments, etc.).

---

## Static RLS Implementation

### Step-by-Step Process

#### Step 1: Create Static Roles in Power BI Desktop

**Creating a Single-Dimension Role:**
1. Open Power BI Desktop with your report
2. Navigate to **Modeling** tab on the ribbon
3. Click **Manage Roles**
4. Click **Create** to create a new role
5. Name the role descriptively (e.g., "France_Country")
6. Select the table to filter (e.g., Dim_Country)
7. Click the three dots (…) next to the table name
8. Select **Add filter** → Choose the column to filter
9. Enter the filter expression: `[Country] = "France"`
10. Click **Save**

**Example: France Country Role**
```DAX
[Country] = "France"
```

> [!NOTE]
> The filter expression uses DAX syntax. The column name is enclosed in square brackets, and text values are in double quotes.

#### Step 2: Create Multi-Dimension Roles

**Filtering Multiple Tables Simultaneously:**
1. Click **Manage Roles** → **Create**
2. Name: "Static_France_Montana"
3. Add filter to Dim_Country: `[Country] = "France"`
4. Add filter to Dim_Product: `[Product] = "Montana"`
5. Click **Save**

**Example Configuration:**
* Table: Dim_Country → Filter: `[Country] = "France"`
* Table: Dim_Product → Filter: `[Product] = "Montana"`

> [!TIP]
> When applying filters to multiple tables, Power BI applies AND logic—users must satisfy all filter conditions.

#### Step 3: Test Roles in Power BI Desktop

**Using "View As" Feature:**
1. Navigate to **Modeling** tab
2. Click **View as**
3. Select the role to test (e.g., "France_Country")
4. Optionally specify **Other user** with an email address
5. Observe the report data filtering
6. Click **Stop viewing** to return to normal view

> [!WARNING]
> Always test roles thoroughly in Desktop before publishing to ensure filters work as expected.

#### Step 4: Publish to Power BI Service

1. Click **Publish** button in Home ribbon
2. Select destination workspace
3. Wait for confirmation: "Success! Your report has been published"
4. Click the link to open in Power BI Service

#### Step 5: Assign Users to Roles in Power BI Service

**Role Assignment Process:**
1. Navigate to your workspace in Power BI Service
2. Locate the dataset (not the report)
3. Click the three dots (…) next to the dataset
4. Select **Security**
5. You'll see all roles created in Desktop
6. Enter user email addresses in the role's text box
7. Click **Add**
8. Click **Save**

**Testing in Power BI Service:**
1. After assigning users, click **Test as role**
2. Select the role and optionally specify a user
3. View the filtered report as that user would see it

> [!IMPORTANT]
> Users must be assigned to roles in Power BI Service. Publishing the report alone does not grant access—explicit role membership is required.

### Static RLS Examples

#### Example 1: Single Country Filter
**Scenario**: Create a role for French regional managers who should only see France data.

**Role Name**: `France_Region`

**Filter Expression:**
```DAX
[Country] = "France"
```

**Result**: Users assigned to this role see only France data across all visuals.

#### Example 2: Multiple Country Filter
**Scenario**: European sales team needs access to multiple European countries.

**Role Name**: `Europe_Region`

**Filter Expression:**
```DAX
[Country] IN {"France", "Germany", "Spain", "Italy"}
```

**Result**: Users see data for all four countries combined.

#### Example 3: Multi-Dimension Filter
**Scenario**: Product managers need access to specific products in specific regions.

**Role Name**: `France_Premium_Products`

**Filters:**
* Dim_Country: `[Country] = "France"`
* Dim_Product: `[ProductCategory] = "Premium"`

**Result**: Users see only Premium products sold in France.

### Limitations of Static RLS

> [!CAUTION]
> Static RLS has significant scalability challenges that should be considered before implementation.

**Key Limitations:**
* **Role Proliferation**: Need separate role for each filter combination
  - 100 countries = 100 roles (if filtering by country only)
  - 100 countries × 10 product categories = 1,000 potential role combinations
* **Maintenance Overhead**: Adding new filter values requires creating new roles
* **Publishing Required**: Every role change requires republishing the dataset
* **User Management**: Assigning users to many specific roles becomes cumbersome
* **Inflexibility**: Cannot easily adapt to organizational changes

**When to Use Static RLS:**
* Small, stable user groups (< 10 roles)
* Fixed organizational structure
* Infrequent changes to access requirements
* Simple, single-dimension filtering needs

---

## Dynamic RLS Implementation

### Core Concepts

#### Security Table
* **Purpose**: Maps user identities to their permitted data values
* **Structure**: Contains at minimum:
  - User email addresses (username@domain.com)
  - Data dimension values (Country, Product, Department, etc.)
* **Source**: Can be Excel, SQL Server, SharePoint, or any supported data source

**Example Security Table:**

| EmailID | Country | Product |
|---------|---------|---------|
| xyz@databools.com | Germany | Montana |
| yzx@databools.com | Mexico | Paseo |
| abc@databools.com | France | VTT |

> [!NOTE]
> The security table should be refreshed regularly to reflect organizational changes in user permissions.

#### USERPRINCIPALNAME() Function
* **DAX Function**: Returns the email address of the currently logged-in user
* **Syntax**: `USERPRINCIPALNAME()`
* **Return Type**: Text (email address in UPN format)
* **Use Case**: Dynamically identifies users for filtering in RLS

**Example:**
```DAX
USERPRINCIPALNAME()
// Returns: "john.doe@company.com" (when John Doe is logged in)
```

> [!IMPORTANT]
> USERPRINCIPALNAME() only works in Power BI Service, not in Power BI Desktop. For Desktop testing, use "Other user" option in "View as" feature.

### Data Model Configuration for Dynamic RLS

#### Model Design Requirements

**For Single-Dimension Dynamic RLS:**
1. Security table connected to dimension table
2. One-to-many relationship from Security to Dimension
3. Filter direction: **Security → Dimension** (single direction)
4. Cross-filter direction: **Single**

**Relationship Chain:**
```
Security Table → Dim_Country → Fact_Sales
```

**Example Relationships:**
* Security[Country] (Many) → Dim_Country[Country] (One)
* Dim_Country[CountryKey] (One) → Fact_Sales[CountryKey] (Many)

> [!TIP]
> Ensure the security table uses the same data type and exact values as the dimension table for proper filtering.

#### Step-by-Step: Single-Dimension Dynamic RLS

**Scenario**: Filter data by Country based on logged-in user

**Step 1: Prepare Security Table**
Create/import a table with this structure:
```
| EmailID               | Country  |
|-----------------------|----------|
| xyz@databools.com     | Germany  |
| yzx@databools.com     | Mexico   |
| abc@databools.com     | France   |
```

**Step 2: Create Relationship**
1. Go to Model View
2. Drag Security[Country] to Dim_Country[Country]
3. Ensure relationship direction: Security → Dim_Country
4. Cardinality: Many-to-One (Many on Security side)

**Step 3: Create Dynamic Role**
1. **Modeling** → **Manage Roles** → **Create**
2. Role Name: `Dynamic_Country`
3. Select **Security** table
4. Click three dots → **Add filter**
5. Filter column: EmailID
6. Enter expression:
```DAX
[EmailID] = USERPRINCIPALNAME()
```
7. Click **Save**

**Step 4: Test in Desktop**
1. **Modeling** → **View as**
2. Select: `Dynamic_Country`
3. Select: **Other user**
4. Enter: `xyz@databools.com`
5. Verify report shows only Germany data

**Step 5: Publish and Assign**
1. Publish to Power BI Service
2. In Service: Dataset → Three dots → **Security**
3. Find `Dynamic_Country` role
4. Add user email: `xyz@databools.com`
5. Click **Add** then **Save**

> [!IMPORTANT]
> Unlike static RLS, you still need to add users to the dynamic role in Power BI Service, but they'll automatically see their specific data based on the security table.

### Advanced: Multi-Dimension Dynamic RLS

**Challenge**: Filter by both Country AND Product simultaneously

#### The Relationship Problem

> [!WARNING]
> Power BI does not allow multiple active relationships from one table to multiple dimension tables when they both filter the same fact table.

**Invalid Model Structure:**
```
        Security Table
       /              \
      ↓                ↓
Dim_Country      Dim_Product
      ↓                ↓
      └──→ Fact_Sales ←┘
```

**Why This Fails:**
* Both Country and Product filter the Fact table
* Creating direct relationships from Security to both creates ambiguous filter paths
* Power BI shows one relationship as dotted (inactive)

#### Solution: DAX-Based Dynamic Filtering

Instead of relationships, use pure DAX expressions to filter multiple dimensions.

**Step 1: Remove Relationships**
1. Delete any relationships from Security table to dimension tables
2. Security table should be standalone (no relationship lines)

**Step 2: Create Multi-Dimension Role with DAX**

**Role Name**: `Dynamic_Country_Product`

**For Dim_Country table:**
```DAX
// Store the logged-in user's email
VAR UserRole = USERPRINCIPALNAME()

// Find the country value from Security table for this user
VAR RLS = 
    CALCULATE(
        VALUES(Security[Country]),
        FILTER(Security, Security[EmailID] = UserRole)
    )

// Filter the Country dimension based on the retrieved value
RETURN
    [Country] IN RLS
```

**For Dim_Product table:**
```DAX
// Store the logged-in user's email
VAR UserRole = USERPRINCIPALNAME()

// Find the product value from Security table for this user
VAR RLS = 
    CALCULATE(
        VALUES(Security[Product]),
        FILTER(Security, Security[EmailID] = UserRole)
    )

// Filter the Product dimension based on the retrieved value
RETURN
    [Product] IN RLS
```

#### DAX Expression Breakdown

**Understanding the Country Filter:**

1. **`VAR UserRole = USERPRINCIPALNAME()`**
   - Captures current user's email (e.g., "xyz@databools.com")
   - Stored in variable for reuse

2. **`FILTER(Security, Security[EmailID] = UserRole)`**
   - Filters Security table to rows matching the logged-in user
   - Example: Returns rows where EmailID = "xyz@databools.com"

3. **`VALUES(Security[Country])`**
   - Extracts unique Country values from filtered Security rows
   - Example: Returns "Germany" for user xyz@databools.com

4. **`CALCULATE(VALUES(...), FILTER(...))`**
   - Evaluates VALUES function in the filtered context
   - Ensures we get country values for the specific user

5. **`[Country] IN RLS`**
   - Filters Dim_Country table where Country column matches RLS variable
   - Boolean expression: TRUE for matching rows, FALSE otherwise

> [!NOTE]
> The IN operator allows for multiple values. If a user has access to multiple countries in the Security table, all matching countries will be included.

**Step 3: Test Multi-Dimension Dynamic RLS**

1. **View as** → Select `Dynamic_Country_Product` role
2. **Other user**: `xyz@databools.com`
3. Verify report shows:
   - Country: Germany only
   - Product: Montana only
4. Test with different user: `yzx@databools.com`
5. Verify report shows:
   - Country: Mexico only
   - Product: Paseo only

**Step 4: Publish and Assign Users**

Same process as single-dimension:
1. Publish to Service
2. Dataset → Security → `Dynamic_Country_Product`
3. Add all users who should have dynamic access
4. Users automatically see their data based on Security table

> [!TIP]
> With multi-dimension dynamic RLS, you only need ONE role regardless of how many users or filter combinations you have. The Security table handles all the mapping.

### Dynamic RLS Examples

#### Example 1: Department-Based Access
**Scenario**: Each department sees only their own data

**Security Table:**
| EmailID | Department |
|---------|------------|
| sales1@company.com | Sales |
| sales2@company.com | Sales |
| marketing1@company.com | Marketing |
| hr1@company.com | HR |

**Role**: `Dynamic_Department`

**DAX Filter on Dim_Department:**
```DAX
[Department] = USERPRINCIPALNAME()
```

Wait, this is incorrect! Let me fix it:

```DAX
VAR UserRole = USERPRINCIPALNAME()
VAR RLS = 
    CALCULATE(
        VALUES(Security[Department]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
RETURN
    [Department] IN RLS
```

#### Example 2: Regional Manager Access
**Scenario**: Regional managers see multiple states in their region

**Security Table:**
| EmailID | State |
|---------|-------|
| west.manager@company.com | California |
| west.manager@company.com | Nevada |
| west.manager@company.com | Oregon |
| east.manager@company.com | New York |
| east.manager@company.com | New Jersey |

**Role**: `Dynamic_Region`

**DAX Filter on Dim_State:**
```DAX
VAR UserRole = USERPRINCIPALNAME()
VAR RLS = 
    CALCULATE(
        VALUES(Security[State]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
RETURN
    [State] IN RLS
```

**Result**: west.manager@company.com sees California, Nevada, and Oregon combined.

#### Example 3: Hierarchical Access
**Scenario**: Managers see their own data plus subordinates' data

**Security Table:**
| EmailID | EmployeeID | ManagerOf |
|---------|------------|-----------|
| john@company.com | 101 | NULL |
| jane@company.com | 102 | 101 |
| bob@company.com | 103 | 102 |

**More Complex DAX Required**: Would use PATHCONTAINS or hierarchical functions to traverse reporting structure.

---

## Best Practices for RLS

### Design Best Practices

#### 1. Security Table Management

> [!IMPORTANT]
> The Security table is the foundation of Dynamic RLS and must be carefully maintained.

**Best Practices:**
* **Single Source of Truth**: Maintain one authoritative Security table
* **Regular Updates**: Sync with HR systems or Azure AD automatically
* **Data Quality**: Ensure email addresses exactly match Azure AD UPN format
* **Comprehensive Coverage**: Include all users who need report access
* **Documentation**: Document the meaning of each column and its values

**Security Table Storage Options:**
* **SQL Server**: Best for enterprise, supports automatic refresh
* **Excel/SharePoint**: Good for smaller organizations, easy to update
* **Azure SQL**: Cloud-based, integrates well with Power BI Service
* **Dataverse**: For organizations using Power Platform extensively

#### 2. Performance Optimization

> [!WARNING]
> Poorly designed RLS can significantly impact report performance, especially with large datasets.

**Optimization Techniques:**
* **Star Schema**: Use proper star schema design with RLS on dimension tables
* **Avoid Calculated Tables**: Don't use calculated tables in RLS expressions
* **Simple DAX**: Keep RLS DAX expressions as simple as possible
* **Indexing**: Ensure Security table columns are indexed in source database
* **DirectQuery Considerations**: RLS adds to query complexity; test performance
* **Aggregations**: Use aggregation tables; RLS applies to both detail and aggregated data

**Performance Testing:**
* Test with realistic user loads
* Use Performance Analyzer in Power BI Desktop
* Monitor query execution times in Power BI Service
* Test with large Security tables (simulate full production scale)

#### 3. Testing Strategy

> [!TIP]
> Thorough testing prevents security breaches and user frustration.

**Comprehensive Testing Checklist:**

**Desktop Testing:**
- [ ] Test each role with "View as" feature
- [ ] Test with multiple user identities (Other user)
- [ ] Verify correct data appears for each user
- [ ] Check that forbidden data is NOT visible
- [ ] Test filter interactions with report slicers
- [ ] Verify calculated measures respect RLS
- [ ] Test with edge cases (new users, inactive users)

**Service Testing:**
- [ ] Assign test users to roles
- [ ] Log in as different test users
- [ ] Verify Service behavior matches Desktop
- [ ] Test on different devices (web, mobile, embedded)
- [ ] Check refresh performance with RLS applied
- [ ] Test sharing scenarios (apps, workspaces)
- [ ] Verify RLS works with APIs and embedded scenarios

#### 4. User Management

**Role Assignment Strategy:**
* **Azure AD Groups**: Assign groups to roles instead of individual users when possible
* **Naming Conventions**: Use clear, descriptive role names
* **Documentation**: Maintain documentation of which users/groups have which access
* **Auditing**: Regularly review role assignments for accuracy
* **Onboarding/Offboarding**: Establish processes for adding/removing user access

**Example Role Naming:**
* Good: `Sales_NorthAmerica_Region`, `Finance_Viewer`, `Executive_AllData`
* Poor: `Role1`, `Test`, `ABC`

#### 5. Security Considerations

> [!CAUTION]
> RLS is not a substitute for proper data governance and security architecture.

**Security Principles:**
* **Defense in Depth**: Use RLS as one layer in multi-layered security
* **Least Privilege**: Grant minimum necessary access
* **Regular Audits**: Review and audit RLS configurations quarterly
* **Sensitive Data**: Consider additional protections for highly sensitive data
* **Data Lineage**: Understand how RLS filters propagate through data model
* **Export Controls**: Be aware RLS doesn't prevent "Export to Excel" (use sensitivity labels)

**Security Table Protection:**
* Don't expose Security table in reports
* Restrict access to underlying Security data source
* Use separate credentials for Security table if possible
* Encrypt Security table at rest and in transit

---

## Troubleshooting Common RLS Issues

### Issue 1: RLS Not Filtering Data

**Symptoms:**
* Users see all data instead of filtered subset
* "View as" shows unfiltered data

**Common Causes & Solutions:**

**Cause 1: Relationship Direction Incorrect**
* **Check**: Verify filter direction in Model View
* **Solution**: Ensure Security table filters Dimension (Single direction: Security → Dimension)

**Cause 2: DAX Expression Error**
* **Check**: Look for errors in Manage Roles dialog
* **Solution**: Verify DAX syntax, test expressions in calculated columns first

**Cause 3: Users Not Assigned to Role**
* **Check**: Verify user membership in Power BI Service → Security
* **Solution**: Add users to appropriate role

**Cause 4: Email Mismatch**
* **Check**: Compare USERPRINCIPALNAME() result with Security table EmailID
* **Solution**: Ensure exact match including case, domain, format

> [!TIP]
> Create a test measure to display USERPRINCIPALNAME() temporarily for debugging: `Test UPN = USERPRINCIPALNAME()`

### Issue 2: Wrong Data Displayed

**Symptoms:**
* User sees someone else's data
* Data is filtered but to wrong values

**Common Causes & Solutions:**

**Cause 1: Security Table Mapping Incorrect**
* **Check**: Verify Security table has correct EmailID-to-Value mappings
* **Solution**: Audit and correct Security table data

**Cause 2: Relationship Ambiguity**
* **Check**: Look for inactive (dotted) relationships in Model View
* **Solution**: Use DAX-based filtering for multi-dimension scenarios

**Cause 3: Multiple Role Assignment**
* **Check**: Verify user isn't assigned to multiple conflicting roles
* **Solution**: RLS uses OR logic across roles; user sees union of all role data

> [!NOTE]
> If a user belongs to multiple roles, they see all data permitted by ANY role (OR logic, not AND).

### Issue 3: Performance Problems

**Symptoms:**
* Slow report loading
* Timeout errors
* High resource consumption

**Common Causes & Solutions:**

**Cause 1: Complex DAX in RLS**
* **Check**: Review RLS DAX expressions for complexity
* **Solution**: Simplify expressions, avoid iterators like FILTER when possible

**Cause 2: Large Security Table**
* **Check**: Count rows in Security table
* **Solution**: Optimize Security table structure, create indexes, reduce granularity

**Cause 3: Multiple Active Relationships**
* **Check**: Review relationship complexity in Model View
* **Solution**: Minimize relationship chains, use star schema

**Cause 4: DirectQuery with RLS**
* **Check**: Query execution time in Performance Analyzer
* **Solution**: Consider Import mode, optimize source database, use aggregations

### Issue 4: "View As" Works but Service Doesn't

**Symptoms:**
* Filtering works correctly in Desktop "View as"
* No filtering or wrong filtering in Power BI Service

**Common Causes & Solutions:**

**Cause 1: Dataset Not Published After Changes**
* **Check**: Compare last modified date of Desktop file vs. Service dataset
* **Solution**: Republish dataset to Service

**Cause 2: Users Not in Role**
* **Check**: Verify role membership in Service Security settings
* **Solution**: Add users to role in Service

**Cause 3: Service Cache**
* **Check**: Clear browser cache
* **Solution**: Hard refresh (Ctrl+F5), try incognito mode

**Cause 4: Email Format Difference**
* **Check**: Compare actual Service UPN with expected format
* **Solution**: Verify Security table uses correct UPN format from Azure AD

---

## Advanced RLS Scenarios

### Scenario 1: Admin Override (Full Access)

**Requirement**: Certain users (admins, executives) should see ALL data

**Solution Approach:**

**Option 1: No Role Assignment**
* Don't assign admin users to any role
* Users without role assignments see all data
* **Limitation**: Requires careful user management

**Option 2: Security Table Wildcard**

**Security Table:**
| EmailID | Country |
|---------|---------|
| admin@company.com | * |
| user1@company.com | France |

**DAX Filter:**
```DAX
VAR UserRole = USERPRINCIPALNAME()
VAR UserCountries = 
    CALCULATE(
        VALUES(Security[Country]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
VAR HasWildcard = 
    COUNTROWS(
        FILTER(UserCountries, Security[Country] = "*")
    ) > 0
RETURN
    IF(
        HasWildcard,
        TRUE,  // Show all data
        [Country] IN UserCountries  // Apply filter
    )
```

> [!TIP]
> The wildcard approach keeps all users in the same role structure, simplifying administration.

### Scenario 2: Time-Based Access

**Requirement**: Users access different data in different time periods

**Security Table:**
| EmailID | Department | ValidFrom | ValidTo |
|---------|------------|-----------|---------|
| user1@company.com | Sales | 2024-01-01 | 2024-12-31 |
| user1@company.com | Marketing | 2025-01-01 | 2025-12-31 |

**DAX Filter:**
```DAX
VAR UserRole = USERPRINCIPALNAME()
VAR Today = TODAY()
VAR ValidDepartments = 
    CALCULATE(
        VALUES(Security[Department]),
        FILTER(
            Security,
            Security[EmailID] = UserRole &&
            Security[ValidFrom] <= Today &&
            Security[ValidTo] >= Today
        )
    )
RETURN
    [Department] IN ValidDepartments
```

### Scenario 3: Manager Self-Service (See Own Data + Team Data)

**Security Table:**
| EmailID | EmployeeID | IsManager |
|---------|------------|-----------|
| manager@company.com | 1001 | Yes |
| employee1@company.com | 1002 | No |

**Employee Table (in model):**
| EmployeeID | Name | ManagerID |
|------------|------|-----------|
| 1001 | Manager Name | NULL |
| 1002 | Employee 1 | 1001 |
| 1003 | Employee 2 | 1001 |

**DAX Filter on Employee Table:**
```DAX
VAR UserRole = USERPRINCIPALNAME()
VAR UserEmployeeID = 
    CALCULATE(
        VALUES(Security[EmployeeID]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
VAR IsUserManager = 
    CALCULATE(
        VALUES(Security[IsManager]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
RETURN
    IF(
        IsUserManager = "Yes",
        // Show own data + subordinates' data
        [EmployeeID] = UserEmployeeID || [ManagerID] = UserEmployeeID,
        // Show only own data
        [EmployeeID] = UserEmployeeID
    )
```

### Scenario 4: Object-Level Security (Hiding Measures/Columns)

> [!NOTE]
> RLS filters rows, not columns or measures. For column/measure security, use perspectives or Tabular Editor to hide objects, or use dynamic measures.

**Dynamic Measure Approach:**
```DAX
Secure Salary Measure = 
VAR UserRole = USERPRINCIPALNAME()
VAR UserAccessLevel = 
    CALCULATE(
        VALUES(Security[AccessLevel]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
RETURN
    IF(
        UserAccessLevel IN {"Admin", "HR"},
        SUM(Employee[Salary]),
        BLANK()  // Hide measure for unauthorized users
    )
```

---

## Comparison: Static vs Dynamic RLS

| Aspect | Static RLS | Dynamic RLS |
|--------|-----------|-------------|
| **Scalability** | Poor (N roles for N filter values) | Excellent (1 role for any number of users) |
| **Maintenance** | High (republish for new roles) | Low (update Security table only) |
| **Complexity** | Low (simple DAX) | Medium (requires Security table and DAX) |
| **Setup Time** | Quick for small deployments | Longer initial setup |
| **Flexibility** | Rigid, predefined filters | Highly flexible, data-driven |
| **Performance** | Slightly better (simpler evaluation) | Good (if properly designed) |
| **Best For** | Small, stable user groups | Large, dynamic user populations |
| **Admin Overhead** | High (manage many roles) | Low (manage one Security table) |
| **Security Table** | Not required | Required |
| **Filter Logic** | Hardcoded in role | Determined at runtime |

> [!IMPORTANT]
> For most enterprise scenarios with more than 10-20 distinct filter combinations, Dynamic RLS is the recommended approach.

---

## Flashcard Q&A Section

### Basic Concepts

**Q1: What is Row-Level Security (RLS)?**
<details>
<summary>Click to reveal answer</summary>

A security feature that restricts data access at the row level based on user roles. Users assigned to a role can only see rows that meet the role's filter criteria.
</details>

**Q2: Where is RLS configured in Power BI?**
<details>
<summary>Click to reveal answer</summary>

RLS roles are created in Power BI Desktop using the "Manage Roles" feature in the Modeling tab. Users are assigned to roles in Power BI Service after publishing.
</details>

**Q3: What are the two main types of RLS?**
<details>
<summary>Click to reveal answer</summary>

1. **Static RLS**: Predefined, hardcoded filters (e.g., Country = "France")
2. **Dynamic RLS**: Runtime filtering based on logged-in user identity using USERPRINCIPALNAME()
</details>

**Q4: What is the role of the Security table in Dynamic RLS?**
<details>
<summary>Click to reveal answer</summary>

The Security table maps user identities (email addresses) to their permitted data values (countries, products, departments, etc.), enabling dynamic filtering based on who is logged in.
</details>

### Implementation Questions

**Q5: How do you test RLS in Power BI Desktop?**
<details>
<summary>Click to reveal answer</summary>

Use the "View as" feature: Modeling tab → View as → Select role(s) to test. For Dynamic RLS, also specify "Other user" with an email address to simulate that user's view.
</details>

**Q6: What DAX function identifies the logged-in user in Dynamic RLS?**
<details>
<summary>Click to reveal answer</summary>

`USERPRINCIPALNAME()` - Returns the email address (User Principal Name) of the currently logged-in user in Power BI Service.
</details>

**Q7: How do you create a multi-dimension Dynamic RLS (filtering by both Country and Product)?**
<details>
<summary>Click to reveal answer</summary>

Use DAX expressions instead of relationships. Write separate filter expressions for each dimension table that:
1. Capture USERPRINCIPALNAME()
2. Look up allowed values in Security table
3. Filter the dimension based on those values

Example for Country:
```DAX
VAR UserRole = USERPRINCIPALNAME()
VAR RLS = CALCULATE(VALUES(Security[Country]), FILTER(Security, Security[EmailID] = UserRole))
RETURN [Country] IN RLS
```
</details>

**Q8: Why can't you create direct relationships from Security table to multiple dimension tables?**
<details>
<summary>Click to reveal answer</summary>

Because both dimensions filter the same fact table, creating ambiguous filter paths. Power BI allows only one active relationship path from Security to Fact. Solution: Use DAX-based filtering instead of relationships.
</details>
