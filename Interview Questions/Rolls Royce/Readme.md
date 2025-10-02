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

### Advanced Questions

**Q9: If a user is assigned to multiple RLS roles, what data do they see?**
<details>
<summary>Click to reveal answer</summary>

They see the **union** (OR logic) of all data permitted by any role they belong to. For example, if Role A shows France and Role B shows Germany, the user sees both France and Germany data combined.
</details>

**Q10: What happens if a user is not assigned to any role?**
<details>
<summary>Click to reveal answer</summary>

Users without role assignments can see **all data** in the report (no filtering applied). This can be used intentionally for admin/executive access or unintentionally as a security gap.
</details>

**Q11: Does RLS work in Power BI Desktop?**
<details>
<summary>Click to reveal answer</summary>

Partially. USERPRINCIPALNAME() doesn't return actual user emails in Desktop, but you can test Dynamic RLS using "View as → Other user" by specifying an email. Static RLS works fully in Desktop testing.
</details>

**Q12: Can RLS prevent users from exporting data?**
<details>
<summary>Click to reveal answer</summary>

No. RLS filters what data users see, but they can still export the filtered data they have access to. For additional protection, use sensitivity labels and information protection policies.
</details>

### Performance & Optimization

**Q13: What impact does RLS have on report performance?**
<details>
<summary>Click to reveal answer</summary>

RLS adds filter evaluation overhead, especially with:
- Complex DAX expressions in roles
- Large Security tables
- DirectQuery connections
- Multiple active relationships

Best practice: Keep DAX simple, use star schema design, and test performance with realistic data volumes.
</details>

**Q14: Should the Security table have relationships to dimension tables?**
<details>
<summary>Click to reveal answer</summary>

**For single-dimension Dynamic RLS**: Yes, create relationship from Security to Dimension with single-direction filtering (Security → Dimension).

**For multi-dimension Dynamic RLS**: No relationships; use pure DAX expressions to avoid ambiguous filter paths.
</details>

**Q15: How can you optimize a large Security table?**
<details>
<summary>Click to reveal answer</summary>

- Index columns in source database (EmailID, filter columns)
- Remove unnecessary columns
- Use integer keys instead of text where possible
- Consider reducing granularity (group-level vs. individual-level)
- Store in fast data source (Azure SQL vs. Excel)
- Schedule optimal refresh times
</details>

### Troubleshooting Questions

**Q16: RLS works in Desktop but not in Power BI Service. What's wrong?**
<details>
<summary>Click to reveal answer</summary>

Common causes:
1. **Users not assigned to role in Service** - Add them via Dataset → Security
2. **Dataset not republished after changes** - Publish again from Desktop
3. **Email format mismatch** - Security table emails must exactly match Azure AD UPN format
4. **Browser cache** - Clear cache or try incognito mode
</details>

**Q17: How do you debug what USERPRINCIPALNAME() returns?**
<details>
<summary>Click to reveal answer</summary>

Create a temporary measure:
```DAX
Debug UPN = USERPRINCIPALNAME()
```
Add it to a card visual, publish, and check what value appears when you view the report. Compare this to your Security table EmailID values.
</details>

**Q18: Why does a user see no data after RLS is applied?**
<details>
<summary>Click to reveal answer</summary>

Possible causes:
1. **No matching record in Security table** - User's email not in table
2. **Email mismatch** - Different format (username@domain.com vs. username@Domain.com)
3. **Blank/NULL values in Security table** - Filter criteria not met
4. **Too restrictive filter** - Multiple filters with AND logic excluding all rows
5. **Data type mismatch** - Security table Country value doesn't match Dimension Country exactly
</details>

### DAX & Expression Questions

**Q19: Write the basic Dynamic RLS filter for filtering by Department.**
<details>
<summary>Click to reveal answer</summary>

**Applied to Dim_Department table:**
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

This:
1. Gets current user's email
2. Filters Security table to that user's rows
3. Extracts Department values they can access
4. Filters Dim_Department to those values
</details>

**Q20: What does the IN operator do in RLS DAX expressions?**
<details>
<summary>Click to reveal answer</summary>

The `IN` operator checks if a column value exists in a table or list. Returns TRUE/FALSE.

**Syntax**: `[Column] IN {value1, value2, ...}` or `[Column] IN TableVariable`

**In RLS**: Filters dimension table to rows where column value matches any value in the RLS variable (which contains allowed values from Security table).

Supports multiple values: If user has access to 3 countries, all 3 will pass the filter.
</details>

---

## Real-World Implementation Scenarios

### Scenario 1: Retail Chain with Regional Managers

**Business Requirement:**
- 500 stores across 50 states
- Regional managers oversee 5-10 states each
- 10 regional managers total
- Each manager sees only their states' sales data
- CEO sees all data

**Solution Design:**

**Security Table Structure:**
```
| EmailID                  | State      |
|--------------------------|------------|
| west.manager@retail.com  | CA         |
| west.manager@retail.com  | NV         |
| west.manager@retail.com  | OR         |
| west.manager@retail.com  | WA         |
| central.manager@retail.com| TX        |
| central.manager@retail.com| OK        |
| ceo@retail.com           | *          |
```

**Model Design:**
```
Security Table (no relationships)
        ↓ (DAX filter)
    Dim_State
        ↓
    Fact_Sales
```

**RLS Role: Regional_Access**

**DAX Filter on Dim_State:**
```DAX
VAR UserRole = USERPRINCIPALNAME()
VAR UserStates = 
    CALCULATE(
        VALUES(Security[State]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
VAR HasFullAccess = 
    COUNTROWS(FILTER(UserStates, Security[State] = "*")) > 0
RETURN
    IF(
        HasFullAccess,
        TRUE,  // CEO sees all
        [State] IN UserStates  // Managers see their states
    )
```

**Benefits:**
- One role handles all 10 managers + CEO
- Adding new managers = adding rows to Security table (no republish)
- Manager transfers = updating Security table rows
- Easy to audit: query Security table to see access rights

### Scenario 2: Healthcare Provider with HIPAA Compliance

**Business Requirement:**
- Patient data must be restricted by assigned care team
- Doctors see their patients only
- Nurses see patients on their ward
- Administrators see aggregate data (no patient details)
- Strict compliance with data privacy regulations

**Solution Design:**

**Security Table Structure:**
```
| EmailID              | AccessType | WardID | DoctorID | PatientID |
|----------------------|------------|--------|----------|-----------|
| dr.smith@hospital.com| Doctor     | NULL   | D001     | NULL      |
| nurse.jones@hospital.com| Nurse   | W101   | NULL     | NULL      |
| admin@hospital.com   | Admin      | NULL   | NULL     | NULL      |
```

**Patient Table (in model):**
```
| PatientID | Name    | WardID | PrimaryDoctorID |
|-----------|---------|--------|-----------------|
| P001      | Patient1| W101   | D001            |
| P002      | Patient2| W102   | D002            |
```

**RLS Role: Healthcare_Access**

**DAX Filter on Patient Table:**
```DAX
VAR UserRole = USERPRINCIPALNAME()
VAR UserAccessType = 
    CALCULATE(
        VALUES(Security[AccessType]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
VAR UserWard = 
    CALCULATE(
        VALUES(Security[WardID]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
VAR UserDoctor = 
    CALCULATE(
        VALUES(Security[DoctorID]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
RETURN
    SWITCH(
        UserAccessType,
        "Doctor", [PrimaryDoctorID] = UserDoctor,
        "Nurse", [WardID] = UserWard,
        "Admin", FALSE  // Admins use separate aggregate report
    )
```

**Additional Security Measures:**
- Separate report for administrators (aggregate data only, no patient details)
- Sensitivity labels on patient-level reports
- Audit logging enabled
- Regular access reviews (quarterly)
- Row-level encryption on source database

> [!CAUTION]
> For healthcare and other highly regulated industries, RLS should be part of a comprehensive security strategy including encryption, audit logging, and regular compliance reviews.

### Scenario 3: Multi-Tenant SaaS Application

**Business Requirement:**
- Single Power BI report embedded in SaaS application
- 1,000+ customer tenants
- Each tenant sees only their own data
- No tenant can see another tenant's data
- Performance must be excellent (< 2 second load time)

**Solution Design:**

**Security Table Structure:**
```
| EmailID                    | TenantID | SubscriptionLevel |
|----------------------------|----------|-------------------|
| user1@tenant-a.com         | TENANT_A | Premium           |
| user2@tenant-a.com         | TENANT_A | Premium           |
| user1@tenant-b.com         | TENANT_B | Standard          |
```

**Model Optimization:**
- **Import Mode** (not DirectQuery) for best performance
- **Aggregation Tables** for common queries
- **Partitioning** by TenantID in source database
- **Incremental Refresh** configured

**RLS Role: Tenant_Isolation**

**DAX Filter on Dim_Customer:**
```DAX
VAR UserRole = USERPRINCIPALNAME()
VAR UserTenant = 
    CALCULATE(
        VALUES(Security[TenantID]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
RETURN
    [TenantID] = UserTenant
```

**Embedding Strategy:**
- Use **Embed for your customers** (app owns data)
- Generate embed tokens with RLS identity (TenantID)
- Token lifetime: 1 hour (balance security and performance)
- Cache embed tokens application-side

**Performance Optimizations:**
1. **Indexed Columns**: TenantID indexed in all tables
2. **Star Schema**: Clean dimension-fact relationships
3. **Measure Optimization**: Pre-aggregated measures
4. **Composite Models**: Import for dimensions, DirectQuery for large fact
5. **Query Reduction**: Limit visuals per page (< 15)

**Monitoring:**
- Performance metrics tracked per tenant
- Slow queries flagged automatically
- Regular capacity usage reviews
- Tenant data size limits enforced

### Scenario 4: Financial Services with Client-Advisor Model

**Business Requirement:**
- Financial advisors see their assigned clients' portfolio data
- Clients see only their own portfolio (self-service portal)
- Compliance officers see all data
- Branch managers see all advisors in their branch
- Regional directors see all branches in their region

**Solution Design:**

**Security Table Structure:**
```
| EmailID                | Role           | BranchID | AdvisorID | ClientID | RegionID |
|------------------------|----------------|----------|-----------|----------|----------|
| advisor1@firm.com      | Advisor        | BR001    | ADV001    | NULL     | NULL     |
| client1@email.com      | Client         | NULL     | NULL      | CLI001   | NULL     |
| manager1@firm.com      | BranchManager  | BR001    | NULL      | NULL     | NULL     |
| director1@firm.com     | RegionalDir    | NULL     | NULL      | NULL     | REG001   |
| compliance@firm.com    | Compliance     | NULL     | NULL      | NULL     | NULL     |
```

**Relationship Table (in model):**
```
| ClientID | AdvisorID | BranchID | RegionID |
|----------|-----------|----------|----------|
| CLI001   | ADV001    | BR001    | REG001   |
| CLI002   | ADV001    | BR001    | REG001   |
| CLI003   | ADV002    | BR002    | REG001   |
```

**RLS Role: FinancialServices_Access**

**DAX Filter on Client Table:**
```DAX
VAR UserRole = USERPRINCIPALNAME()
VAR UserRoleType = 
    CALCULATE(
        VALUES(Security[Role]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
VAR UserBranch = 
    CALCULATE(
        VALUES(Security[BranchID]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
VAR UserAdvisor = 
    CALCULATE(
        VALUES(Security[AdvisorID]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
VAR UserClient = 
    CALCULATE(
        VALUES(Security[ClientID]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
VAR UserRegion = 
    CALCULATE(
        VALUES(Security[RegionID]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
RETURN
    SWITCH(
        UserRoleType,
        "Client", [ClientID] = UserClient,
        "Advisor", [AdvisorID] = UserAdvisor,
        "BranchManager", [BranchID] = UserBranch,
        "RegionalDir", [RegionID] = UserRegion,
        "Compliance", TRUE,  // See all data
        FALSE  // Default: no access
    )
```

**Audit & Compliance Features:**
- All data access logged with user identity and timestamp
- Quarterly access reviews required
- Automated alerts for unusual access patterns
- Separate audit report showing who accessed what data
- Data masking for sensitive fields (SSN, account numbers) except for Compliance role

**Object-Level Security (Complementary to RLS):**

**Sensitive Measure (visible only to Compliance):**
```DAX
Client SSN = 
VAR UserRole = USERPRINCIPALNAME()
VAR UserRoleType = 
    CALCULATE(
        VALUES(Security[Role]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
RETURN
    IF(
        UserRoleType = "Compliance",
        SELECTEDVALUE(Client[SSN]),
        "***-**-****"  // Masked for non-Compliance users
    )
```

---

## Integration with Other Power BI Features

### RLS and Power BI Apps

**How RLS Works in Apps:**
- Apps respect RLS configured on underlying datasets
- Users must be assigned to roles even if they have app access
- App permissions + RLS = combined security

**Publishing Workflow:**
1. Configure RLS in dataset
2. Assign users to roles
3. Create app from workspace
4. Add users to app audience
5. Users need BOTH app access AND role membership

> [!IMPORTANT]
> App access alone doesn't grant data visibility. Users must be assigned to appropriate RLS roles separately.

**Best Practice for Apps:**
- Use Azure AD groups for both app access and RLS roles
- Sync group membership regularly
- Document which groups map to which roles
- Test with representative users before general release

### RLS and Dataflows

**Scenario**: Using dataflows as data source with RLS downstream

**Considerations:**
- RLS applied to dataset, not dataflow
- Dataflow loads all data; RLS filters during report consumption
- Security table can come from same or different dataflow
- Schedule dataflow refresh before dataset refresh

**Example Architecture:**
```
Dataflow 1: Business Data (Sales, Products, Customers)
    ↓
Dataflow 2: Security Table (User-to-Data Mappings)
    ↓
Power BI Dataset (RLS configured here)
    ↓
Power BI Report (RLS enforced)
```

### RLS and Paginated Reports

**RLS Support in Paginated Reports:**
- Paginated reports respect RLS when connected to Power BI datasets
- Must explicitly pass user context using Power BI dataset connection
- DirectQuery to SQL: Implement RLS at SQL level instead

**Configuration:**
1. Create paginated report connected to Power BI dataset
2. Use Power BI dataset as data source (not SQL directly)
3. RLS automatically applies based on logged-in user
4. Test with different user identities

**Limitations:**
- Email subscriptions run with subscription owner's identity (not recipient's)
- Data-driven subscriptions can include recipient-specific filtering

### RLS and Power BI Embedded

**Embedding Scenarios:**

**Scenario 1: Embed for Your Organization**
- Users authenticate with Azure AD
- USERPRINCIPALNAME() returns their actual email
- RLS works exactly like Power BI Service
- Assign users to roles in dataset security

**Scenario 2: Embed for Your Customers (App Owns Data)**
- Users don't have Power BI licenses
- Application passes effective identity to Power BI
- Must specify username and roles in embed token

**Embed Token with RLS:**
```csharp
var effectiveIdentity = new EffectiveIdentity(
    username: "user1@tenant-a.com",
    roles: new List<string> { "Tenant_Isolation" },
    datasets: new List<string> { datasetId }
);

var embedToken = await client.Reports.GenerateTokenInGroupAsync(
    workspaceId,
    reportId,
    new GenerateTokenRequest(
        accessLevel: "View",
        identities: new List<EffectiveIdentity> { effectiveIdentity }
    )
);
```

**Effective Identity Behavior:**
- USERPRINCIPALNAME() returns the username specified in embed token
- RLS evaluates based on this identity
- Each embed token can have different identity

> [!WARNING]
> Application is responsible for authentication and authorization. Power BI trusts the identity provided in the embed token. Ensure your application properly authenticates users before generating tokens.

### RLS and Excel "Analyze in Excel"

**How It Works:**
- "Analyze in Excel" respects RLS
- User sees same filtered data as in Power BI Service
- Creates pivot table/charts in Excel with RLS applied

**Limitations:**
- Requires Excel 2016 or later
- Must install Power BI Publisher for Excel
- ODC connection file includes user context
- Cannot override RLS in Excel (security maintained)

**Testing:**
1. Open report in Power BI Service
2. Click "Analyze in Excel" from report menu
3. Download and open .odc file
4. Verify data matches Power BI Service view
5. Create pivot table—RLS remains enforced

---

## Security Best Practices & Compliance

### Defense in Depth Strategy

> [!IMPORTANT]
> RLS should be one layer in a multi-layered security architecture, not the only security control.

**Security Layers:**

1. **Network Security**
   - Azure Virtual Network integration
   - Private endpoints for Power BI
   - Firewall rules at data source level

2. **Identity & Access Management**
   - Azure AD authentication required
   - Multi-factor authentication (MFA) enforced
   - Conditional access policies
   - Just-in-time (JIT) access for admins

3. **Data Security**
   - Encryption at rest (data sources)
   - Encryption in transit (TLS 1.2+)
   - Database-level security (in addition to RLS)
   - Column-level security for sensitive fields

4. **Application Security**
   - RLS (row-level filtering)
   - Sensitivity labels (Microsoft Information Protection)
   - Export controls (disable if required)
   - Workspace access controls

5. **Monitoring & Auditing**
   - Activity logs reviewed regularly
   - Anomaly detection enabled
   - Access reviews (quarterly minimum)
   - Incident response plan documented

### Data Governance Framework

**RLS Governance Checklist:**

- [ ] **Documentation**
  - RLS design document maintained
  - Role definitions documented
  - Data classification policy established
  - User access matrix current

- [ ] **Access Management**
  - Role assignment process defined
  - Approval workflow implemented
  - Onboarding/offboarding procedures
  - Regular access reviews scheduled

- [ ] **Monitoring**
  - Activity monitoring configured
  - Audit log retention policy set
  - Alert rules for suspicious activity
  - Dashboard showing RLS usage metrics

- [ ] **Testing**
  - Test plan documented
  - Test cases for each role
  - Automated testing where possible
  - Penetration testing periodically

- [ ] **Compliance**
  - Regulatory requirements identified (GDPR, HIPAA, etc.)
  - Compliance gaps addressed
  - Regular compliance audits
  - Data subject access request (DSAR) process

### Audit Logging for RLS

**What to Log:**
- User identity accessing report
- Timestamp of access
- Report/dataset accessed
- Filters applied (from RLS)
- Data exported (if allowed)
- Role memberships at time of access

**Implementation Options:**

**Option 1: Power BI Activity Log**
```powershell
# PowerShell script to extract activity logs
Get-PowerBIActivityEvent -StartDateTime '2024-01-01' -EndDateTime '2024-01-31' | 
    Where-Object {$_.Activity -eq 'ViewReport'} |
    Select-Object UserId, Activity, ItemName, DatasetName, Timestamp
```

**Option 2: Azure Log Analytics**
- Connect Power BI to Azure Log Analytics
- Create custom queries for RLS-related events
- Set up alerts for anomalies
- Build compliance dashboard

**Option 3: Source Database Logging**
- Implement auditing at SQL Server level
- Use temporal tables for change tracking
- Log all data access with user context
- Correlate with Power BI activity

**Sample Audit Query:**
```sql
SELECT 
    audit_log.UserId,
    audit_log.AccessTime,
    audit_log.ReportName,
    audit_log.FilteredValues,
    security_table.EmailID,
    security_table.AllowedCountries
FROM AuditLog audit_log
INNER JOIN SecurityTable security_table
    ON audit_log.UserId = security_table.EmailID
WHERE audit_log.AccessTime BETWEEN @StartDate AND @EndDate
ORDER BY audit_log.AccessTime DESC
```

### GDPR Compliance Considerations

**Right to Access (DSAR):**
- Document which reports contain personal data
- Ability to identify all data visible to specific user
- Export user's RLS-filtered view for verification

**Right to Erasure:**
- Process to remove user from Security table
- Verify user removed from all role assignments
- Archive or delete data subject's records

**Right to Data Portability:**
- Allow users to export their filtered data
- Provide data in machine-readable format (CSV, Excel)

**Data Minimization:**
- Only include necessary user data in Security table
- Regularly review and remove obsolete access grants
- Limit data retention periods

**Privacy by Design:**
- RLS built into reports from inception
- Default to most restrictive access
- Explicit consent for broader access

---

## Performance Optimization Deep Dive

### Query Performance Analysis

**Using Performance Analyzer:**

1. **In Power BI Desktop:**
   - View tab → Performance Analyzer → Start Recording
   - Apply RLS: View as → Select role
   - Interact with report (filters, slicers, page navigation)
   - Stop Recording and review results

2. **Key Metrics:**
   - DAX query time
   - Visual display time
   - Other time
   - Total time per visual

3. **Identify Bottlenecks:**
   - Long DAX query times → Optimize RLS expressions or model
   - Long visual display times → Reduce visual complexity
   - Consistent slowness → Data volume or relationship issues

**Performance Analyzer Example Output:**
```
Visual: Sales by Country Table
├─ DAX Query: 2,450 ms  ← High! Investigate RLS filter
├─ Visual Display: 150 ms
└─ Other: 50 ms
Total: 2,650 ms

Visual: Sales by Product Chart
├─ DAX Query: 380 ms  ← Good performance
├─ Visual Display: 120 ms
└─ Other: 45 ms
Total: 545 ms
```

> [!WARNING]
> DAX query times over 1,000ms indicate performance problems. Investigate RLS expressions, relationships, and data model design.

### Optimization Techniques

#### Technique 1: Simplify RLS DAX

**Before (Complex):**
```DAX
VAR UserRole = USERPRINCIPALNAME()
VAR UserCountries = 
    CALCULATETABLE(
        DISTINCT(Security[Country]),
        FILTER(
            ALL(Security),
            Security[EmailID] = UserRole
        )
    )
VAR Result = 
    FILTER(
        VALUES(DimCountry[Country]),
        DimCountry[Country] IN UserCountries
    )
RETURN
    COUNTROWS(Result) > 0
```

**After (Simplified):**
```DAX
VAR UserRole = USERPRINCIPALNAME()
VAR RLS = 
    CALCULATE(
        VALUES(Security[Country]),
        FILTER(Security, Security[EmailID] = UserRole)
    )
RETURN
    [Country] IN RLS
```

**Impact**: 40-60% reduction in evaluation time

#### Technique 2: Use Integer Keys Instead of Text

**Before:**
```
Security Table:
| EmailID | Country  |
|---------|----------|
| user1   | Germany  |

DimCountry:
| Country | Sales |
|---------|-------|
| Germany | 1000  |
```

**After:**
```
Security Table:
| EmailID | CountryKey |
|---------|------------|
| user1   | 101        |

DimCountry:
| CountryKey | Country | Sales |
|------------|---------|-------|
| 101        | Germany | 1000  |
```

**RLS Filter Changed From:**
```DAX
[Country] IN RLS  // Text comparison
```

**To:**
```DAX
[CountryKey] IN RLS  // Integer comparison (faster)
```

**Impact**: 20-30% faster filtering, especially with large dimension tables

#### Technique 3: Pre-Aggregate Data

**Scenario**: Dashboard shows summary metrics (Total Sales, Average Order Value)

**Without Aggregations:**
- RLS filters millions of fact rows every query
- Every user query processes full fact table
- Slow performance, high resource usage

**With Aggregations:**
```
Aggregation Table: Sales_By_Country_Month
| CountryKey | YearMonth | TotalSales | OrderCount |
|------------|-----------|------------|------------|
| 101        | 202401    | 500000     | 1250       |
| 102        | 202401    | 350000     | 875        |
```

**Configuration:**
1. Create aggregation table at Country-Month grain
2. Configure as aggregation in Power BI
3. RLS applied to aggregation table (fewer rows)
4. Queries automatically use aggregation when possible

**Impact**: 5-10x faster queries for summary visuals

#### Technique 4: Incremental Refresh with RLS

**Challenge**: Large fact tables (100M+ rows) slow to refresh and query

**Solution:**
1. Configure incremental refresh on fact table
2. Partition by date (e.g., monthly partitions)
3. RLS filters partitions before evaluation
4. Older data loaded once, newer data refreshed incrementally

**Configuration:**
- Modeling → Incremental Refresh → Configure policy
- Refresh rows where Date is in last 12 months
- Store 5 years of data
- RLS applies across all partitions

**Impact**: 80-90% reduction in refresh time, better query performance

### Monitoring Performance in Production

**Key Metrics to Track:**

1. **Query Duration P95 (95th percentile)**
   - Target: < 3 seconds for interactive reports
   - Alert if exceeds 5 seconds consistently

2. **Failed Queries**
   - Target: < 0.1% failure rate
   - Investigate timeouts and errors

3. **Concurrent Users**
   - Monitor peak usage times
   - Plan capacity accordingly

4. **Dataset Size**
   - Track growth over time
   - Optimize or archive before hitting limits

5. **RLS-Specific Metrics:**
   - Average rows per user (post-RLS filtering)
   - Security table size and refresh duration
   - Role assignment distribution

**Monitoring Dashboard Example:**

```DAX
// Measure: Average Query Duration
Avg Query Duration = 
AVERAGE(ActivityLog[QueryDurationMs]) / 1000

// Measure: % Slow Queries (> 3s)
Slow Query Percentage = 
VAR SlowQueries = 
    COUNTROWS(FILTER(ActivityLog, ActivityLog[QueryDurationMs] > 3000))
VAR TotalQueries = COUNTROWS(ActivityLog)
RETURN
DIVIDE(SlowQueries, TotalQueries, 0) * 100

// Measure: Active Users with RLS
Users With RLS = 
DISTINCTCOUNT(ActivityLog[UserId])
```

---

## Migration Strategies

### Migrating from Static to Dynamic RLS

**When to Migrate:**
- Number of static roles exceeds 20-30
- Frequent role changes required
- New filter dimensions added
- User management becoming unmanageable

**Migration Process:**

**Phase 1: Preparation (2-4 weeks)**

1. **Document Current State**
   - List all existing static roles
   - Map users to roles
   - Document filter criteria for each role
   - Identify edge cases and exceptions

2. **Design Security Table**
   - Determine Security table structure
   - Identify data source (SQL, Excel, SharePoint)
   - Plan refresh schedule
   - Design for future extensibility

   **Example Mapping:**
   ```
   Static Role "France_Sales" (5 users) 
   →
   Security Table Rows:
   | EmailID | Country | Department |
   |---------|---------|------------|
   | user1   | France  | Sales      |
   | user2   | France  | Sales      |
   | user3   | France  | Sales      |
   | user4   | France  | Sales      |
   | user5   | France  | Sales      |
   ```

3. **Build Security Table**
   - Extract user assignments from existing roles
   - Add additional context (department, region, etc.)
   - Validate data quality (no duplicates, proper email format)
   - Set up automated refresh

**Phase 2: Implementation (1-2 weeks)**

4. **Update Data Model**
   - Import Security table
   - Create relationships (or plan DAX approach)
   - Test relationships in Desktop
   - Validate filter propagation

5. **Create Dynamic Roles**
   - Build single dynamic role per dimension
   - Use DAX expressions with USERPRINCIPALNAME()
   - Test thoroughly with representative users
   - Document DAX logic

6. **Parallel Testing**
   - Keep static roles active
   - Add dynamic roles
   - Assign small user group to dynamic roles
   - Compare results (should be identical)
   - Fix any discrepancies

**Phase 3: Cutover (1 week)**

7. **User Communication**
   - Notify users of upcoming change
   - Explain benefits (no functional change to them)
   - Provide support contact information
   - Schedule migration during low-usage period

8. **Execute Migration**
   - Publish updated dataset with dynamic roles
   - Assign all users to appropriate dynamic roles
   - Remove static role assignments
   - Monitor for issues

9. **Post-Migration**
   - Verify all users have access
   - Monitor performance metrics
   - Collect user feedback
   - Document lessons learned
   - Delete old static roles after 30-day grace period

**Rollback Plan:**
- Keep static roles for 30 days
- Document rollback procedure
- Test rollback in non-production environment
- Criteria for rollback (e.g., > 10% users affected)

### Migrating RLS Across Environments

**Scenario**: Promote RLS from Dev → Test → Production

**Challenge**: Role memberships differ across environments

**Solution: Parameterized Approach**

**Development Environment:**
- Create and test RLS roles
- Use test user accounts
- Security table points to dev data source
