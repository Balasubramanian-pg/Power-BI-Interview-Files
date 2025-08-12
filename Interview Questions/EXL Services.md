# **Detailed Explanation of Power BI Interview Questions from EXL Services**

This video covers **two critical Power BI interview questions** recently asked at **EXL Services**, focusing on **data transformation** and **report sharing**. Below is a **comprehensive breakdown** of each scenario, including **problem statements, solutions, step-by-step execution, and potential pitfalls**.

---

## **Question 1: Calculating Net Sales with Discount Percentage**

### **Problem Statement**

You have a sales dataset with:

- **Product Price** (numeric, e.g., `$100`)
- **Discount** (text with percentage, e.g., `"30%"`)

**Goal:**

Calculate **Net Sales** after applying the discount.

### **Challenges**

1. **Discount Column is Text-Based**
    - Contains `%` symbol (e.g., `"30%"`).
    - Some entries may **lack the % sign** (e.g., `"30"` or `"30% Discount"`).
    - Using **"Split by Delimiter"** fails if data is inconsistent.
2. **Need to Convert Text to Numeric Value**
    - Extract **only the numeric part** (e.g., `30` from `"30%"`).
    - Apply formula:
        
        ```Plain
        Net Sales = (1 - Discount/100) * Product Price
        ```
        

---

### **Solution: Using Power Query’s "Column from Examples"**

### **Step-by-Step Execution**

1. **Open Power Query Editor**
    - Right-click the table → **"Transform Data"**
2. **Extract Numeric Discount**
    - Select the **Discount column** → **"Add Column"** → **"Column from Examples"**
    - Manually type the **expected output** (e.g., for `"30%"`, enter `30`).
    - Power BI **automatically detects the pattern** and extracts numbers.
3. **Generated M Code (Behind the Scenes)**
    
    ```Plain
    = Table.AddColumn(
        Source,
        "Discount Percent",
        each Text.Select([Discount], {"0".."9"}),
        type number
    )
    ```
    
    - `**Text.Select()**` keeps only digits (`0-9`).
    - Converts the result to a **numeric column**.
4. **Calculate Net Sales**
    - Add a **Custom Column**:
        
        ```Plain
        Net Sales = (1 - [Discount Percent]/100) * [Product Price]
        ```
        

### **Illustrative Example**

|   |   |   |   |
|---|---|---|---|
|Product Price|Discount|Discount Percent (Extracted)|Net Sales|
|100|30%|30|70|
|200|15%|15|170|
|150|10% Off|10|135|

### **Why This Works**

- **"Column from Examples"** uses **AI-based pattern recognition** to handle inconsistencies.
- `**Text.Select()**` ensures only numbers are extracted, even if the format varies.

---

## **Question 2: Sharing Power BI Reports with External Users**

### **Problem Statement**

- You need to **share a Power BI report** with users **outside your organization’s domain**.
- Standard sharing methods (e.g., **"Share" button in Power BI Service**) only work for internal users.

### **Solution: "Publish to Web" (With Security Risks)**

### **Step-by-Step Execution**

1. **Publish the Report to Power BI Service**
    - In Power BI Desktop → **"Publish"** → Select workspace.
2. **Generate a Public Embed Link**
    - Open the report in **Power BI Service**.
    - Click **File** → **Embed report** → **"Publish to web"**.
    - Click **"Create embed code"** (requires **admin approval**).
3. **Copy & Share the Link**
    - A **public URL** is generated (e.g., `https://app.powerbi.com/view?r=eyJr...`).
    - Anyone with this link can **view the report without logging in**.

### **Security Risks & Limitations**

✅ **Pros:**

- Works for **external users** (no Power BI license needed).
- Easy to share (just send a link).

❌ **Cons:**

- **No access control** – anyone with the link can view data.
- **Potential data leakage** if the link is shared publicly.
- **Not compliant** with strict data governance policies (e.g., GDPR).

### **Alternative Secure Methods**

|   |   |   |
|---|---|---|
|Method|Description|Best For|
|**Power BI Apps**|Distribute via an internal app (requires sign-in).|Internal users|
|**Azure B2B Guest Access**|Invite external users via email (Microsoft account required).|Trusted partners|
|**Row-Level Security (RLS)**|Restrict data visibility per user.|Sensitive data|

---

## **Final Comparison of Solutions**

|   |   |   |   |
|---|---|---|---|
|Scenario|Best Approach|Alternative|Risk Level|
|**Extracting Numbers from Text**|`Column from Examples`|`Text.Select()` in M|Low|
|**Sharing Reports Externally**|`Publish to Web` (if public access is okay)|`Azure B2B Guest Access` (secure)|High (if public)|

---

## **Key Takeaways**

1. **For Data Cleaning:**
    - **"Column from Examples"** is powerful for **pattern-based extraction**.
    - `**Text.Select()**` is useful for **removing non-numeric characters**.
2. **For Report Sharing:**
    - **"Publish to Web"** is quick but **risky for sensitive data**.
    - **Azure B2B or RLS** are better for **secure external sharing**.

  

# **Structured Solutions for Sharing Power BI Reports with External Clients**

Below are **two secure and structured methods** to share Power BI dashboards with clients who have email domains different from your organization's domain.

---

## **Method 1: Create Test Accounts in Your Domain & Use Publish to Portal**

_(Best for temporary or demo access)_

### **Steps to Implement**

1. **Create a Test Account**
    - In **Azure Active Directory (AAD)** or **Microsoft 365 Admin Center**, create a new user account with:
        - **Email:** `clientname@yourcompany.com` (e.g., `clientA@yourorg.com`)
        - **Temporary Password:** Set a strong password.
2. **Share the Dashboard via Power BI Portal**
    - Publish the report to **Power BI Service**.
    - Go to **"Workspace" → "Access"** → Add the test account (`clientA@yourorg.com`).
    - Assign **appropriate roles** (e.g., "Viewer").
3. **Provide Login Credentials Securely**
    - Share the credentials via a **secure channel** (e.g., encrypted email or password manager).
    - Instruct the client to **change the password** on first login.

### **Pros & Cons**

|   |   |
|---|---|
|✅ **Advantages**|❌ **Disadvantages**|
|✔ No external domain restrictions.|✖ Clients must remember a separate login.|
|✔ Full control over access.|✖ Not ideal for long-term collaboration.|
|✔ Works with **Publish to Web** (if needed).|✖ Requires manual account management.|

---

## **Method 2: Invite External Users via Azure B2B & Apply Object-Level Security (OLS)**

_(Best for long-term, secure collaboration)_

### **Steps to Implement**

1. **Admin Adds External User to Azure AD**
    - The **Workspace Admin** goes to **Azure AD → "Users" → "New Guest User"**.
    - Enter the client’s **actual email** (e.g., `client@gmail.com`).
    - Assign **"Guest"** role with restricted permissions.
2. **Invite the User to Power BI Workspace**
    - In **Power BI Service**, go to **Workspace → "Access"**.
    - Add the guest user (`client@gmail.com`) with **"Viewer"** access.
3. **Implement Row-Level Security (RLS) or Object-Level Security (OLS)** _(Optional)_
    - Define **data filters** in Power BI to restrict what the client can see.
    - Example:
        
        ```Plain
        // RLS Example: Only show data where Region = "ClientA"
        [Region] = USERNAME()
        ```
        

### **Pros & Cons**

|   |   |
|---|---|
|✅ **Advantages**|❌ **Disadvantages**|
|✔ Clients use **their own email** (no extra logins).|✖ Requires **Azure AD admin access**.|
|✔ Supports **fine-grained security (RLS/OLS)**.|✖ Slightly more setup than test accounts.|
|✔ Scalable for **multiple external users**.|✖ Client must accept an **invitation email**.|

---

## **Comparison Table: Which Method to Use?**

|   |   |   |
|---|---|---|
|Feature|**Test Account + Publish to Portal**|**Azure B2B + RLS/OLS**|
|**Ease of Setup**|⭐⭐⭐ (Simple)|⭐⭐ (Requires Admin)|
|**Security**|⭐⭐ (Shared Credentials)|⭐⭐⭐ (Secure, No Password Sharing)|
|**Long-Term Usability**|⭐ (Temporary)|⭐⭐⭐ (Permanent)|
|**Best For**|Demos, short-term access|Ongoing client collaboration|

---

## **Final Recommendation**

- **For quick demos/temporary access** → Use **Test Accounts + Publish to Portal**.
- **For long-term, secure collaboration** → Use **Azure B2B + RLS/OLS**.