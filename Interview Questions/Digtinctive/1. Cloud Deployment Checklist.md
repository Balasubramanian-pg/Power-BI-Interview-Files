# Power BI Service Cloud Deployment Checklist

## Pre-Deployment Phase

### Environment Setup
- [ ] Verify Power BI Service license type (Pro, Premium Per User, Premium, or Fabric)
- [ ] Confirm workspace capacity and allocation
- [ ] Identify tenant administrator and workspace administrators
- [ ] Review organizational governance policies and compliance requirements
- [ ] Set up development, testing, and production workspaces
- [ ] Document data residency requirements and regional constraints

> [!IMPORTANT]
> Ensure you have the appropriate Power BI license for your deployment needs. Pro licenses are required for sharing content, while Premium capacities provide dedicated resources and advanced features.

> [!TIP]
> Use separate workspaces for development, testing, and production to maintain proper version control and prevent accidental changes to live reports.

### Security & Access Management
- [ ] Define user roles and permissions (Admin, Member, Contributor, Viewer)
- [ ] Configure Row-Level Security (RLS) if required
- [ ] Set up Azure Active Directory (Entra ID) security groups
- [ ] Review and configure sensitivity labels
- [ ] Enable Multi-Factor Authentication (MFA) for admin accounts
- [ ] Document data access policies and approval workflows

> [!WARNING]
> Always test Row-Level Security (RLS) thoroughly before deployment. Incorrect RLS configuration can expose sensitive data to unauthorized users.

> [!TIP]
> Use Azure AD security groups instead of individual user assignments for easier management and scalability.

### Data Source Configuration
- [ ] Identify all data sources (on-premises, cloud, hybrid)
- [ ] Configure On-Premises Data Gateway if needed
- [ ] Test data source connectivity and credentials
- [ ] Set up service principal or OAuth authentication where applicable
- [ ] Verify firewall rules and network connectivity
- [ ] Document connection strings and authentication methods (encrypted storage)

> [!CAUTION]
> Never hardcode passwords or connection strings in Power BI reports. Always use secure credential storage methods and gateway configurations.

> [!IMPORTANT]
> Test all data source connections in the target environment before go-live. Connection issues are the most common cause of deployment failures.

### Report Optimization
- [ ] Optimize data model (remove unnecessary columns, optimize relationships)
- [ ] Implement incremental refresh where appropriate
- [ ] Review and optimize DAX measures for performance
- [ ] Reduce visual count and complexity on report pages
- [ ] Test report performance with production-level data volumes
- [ ] Compress large datasets and remove unused tables

> [!TIP]
> Use Performance Analyzer in Power BI Desktop to identify slow-loading visuals and optimize them before deployment.

> [!NOTE]
> Reports with more than 30-40 visuals per page may experience performance issues. Consider splitting complex pages into multiple report pages.

## Deployment Phase

### Publishing Process
- [ ] Publish report from Power BI Desktop to target workspace
- [ ] Verify all visuals render correctly in the service
- [ ] Configure dataset refresh schedule
- [ ] Test scheduled refresh execution
- [ ] Set up refresh failure notifications
- [ ] Document deployment date and version number

> [!WARNING]
> Always publish to a test workspace first before deploying to production. This helps catch any service-specific rendering or performance issues.

> [!IMPORTANT]
> Configure refresh failure notifications immediately after deployment to ensure you're alerted to any data refresh issues.

### Gateway Configuration (if applicable)
- [ ] Install and configure On-Premises Data Gateway
- [ ] Register gateway with Power BI Service
- [ ] Add data source credentials to gateway
- [ ] Test gateway connectivity and data refresh
- [ ] Configure gateway recovery key and backup
- [ ] Set up gateway administrators

> [!CAUTION]
> Store the gateway recovery key in a secure location immediately after installation. Without it, you cannot recover or migrate the gateway.

> [!TIP]
> Install the gateway on a dedicated server (not a personal workstation) to ensure consistent availability and performance.

> [!NOTE]
> Gateway requires .NET Framework 4.7.2 or later and should be installed in a location with stable network connectivity to both data sources and Power BI Service.

### Workspace Settings
- [ ] Configure workspace access and permissions
- [ ] Enable workspace apps if creating end-user applications
- [ ] Set up workspace contact information
- [ ] Configure workspace description and metadata
- [ ] Enable audit logging for the workspace
- [ ] Link workspace to Premium capacity (if applicable)

> [!TIP]
> Use Power BI Apps instead of sharing workspaces directly with end users. Apps provide a cleaner, more controlled user experience.

## Post-Deployment Phase

### Testing & Validation
- [ ] Perform end-to-end testing with production data
- [ ] Validate all filters, slicers, and drill-through functionality
- [ ] Test report performance under concurrent user load
- [ ] Verify Row-Level Security works correctly for all user groups
- [ ] Test mobile view and mobile app rendering
- [ ] Validate all bookmarks and navigation elements
- [ ] Check all calculated columns and measures return expected results

> [!WARNING]
> Test RLS by using "View as" feature in Power BI Service for each security role. Do not rely solely on desktop testing.

> [!IMPORTANT]
> Conduct user acceptance testing (UAT) with actual business users before announcing general availability.

> [!TIP]
> Test reports on mobile devices to ensure responsive design works correctly. What looks good on desktop may not translate well to mobile.

### User Enablement
- [ ] Create and share user documentation or training materials
- [ ] Conduct user training sessions (if required)
- [ ] Share workspace or app with intended audiences
- [ ] Provide access instructions and support contact information
- [ ] Set up feedback mechanism for users
- [ ] Create quick reference guides or video tutorials

> [!NOTE]
> User adoption increases significantly when proper training and documentation are provided. Invest time in creating clear, accessible resources.

### Monitoring & Maintenance
- [ ] Set up dataset refresh monitoring
- [ ] Configure alerts for refresh failures
- [ ] Enable usage metrics for reports and datasets
- [ ] Schedule regular review of capacity metrics
- [ ] Set up monitoring for gateway health (if applicable)
- [ ] Document escalation procedures for issues

> [!IMPORTANT]
> Enable usage metrics to understand how users interact with your reports. This data is invaluable for optimization and future enhancements.

> [!TIP]
> Set up Power Automate flows to send notifications to Teams or email when refresh failures occur, ensuring quick response to issues.

### Documentation
- [ ] Document data model structure and relationships
- [ ] Create technical documentation for DAX measures
- [ ] Document refresh schedule and dependencies
- [ ] Maintain version control and change log
- [ ] Document known limitations or issues
- [ ] Create data dictionary for business users
- [ ] Store connection details and credentials securely

> [!NOTE]
> Comprehensive documentation is crucial for maintenance and knowledge transfer. Future developers (including yourself) will thank you.

> [!TIP]
> Use Git integration in Power BI Desktop to maintain version history of your PBIX files automatically.

## Governance & Compliance

### Compliance Checks
- [ ] Verify compliance with data protection regulations (GDPR, CCPA, etc.)
- [ ] Ensure proper data classification and labeling
- [ ] Review and apply information protection policies
- [ ] Verify data retention policies are configured
- [ ] Document audit trail and access logs
- [ ] Confirm backup and disaster recovery procedures

> [!CAUTION]
> Failure to comply with data protection regulations can result in significant fines and legal consequences. Consult with your legal and compliance teams.

> [!IMPORTANT]
> Enable audit logging in the Power BI admin portal to track all user activities for compliance and security purposes.

### Best Practices
- [ ] Implement naming conventions for reports, datasets, and workspaces
- [ ] Use deployment pipelines for dev/test/prod promotion
- [ ] Set up version control for Power BI Desktop files (Git integration)
- [ ] Configure automatic page refresh settings appropriately
- [ ] Review and optimize query folding for data sources
- [ ] Document support and maintenance responsibilities

> [!TIP]
> Deployment pipelines automate the promotion process between dev, test, and production, reducing manual errors and saving time.

> [!NOTE]
> Consistent naming conventions make it easier to manage reports at scale and help users find the content they need.

## Ongoing Operations

### Regular Reviews
- [ ] Weekly: Monitor refresh failures and performance issues
- [ ] Monthly: Review usage metrics and user adoption
- [ ] Quarterly: Assess capacity utilization and scaling needs
- [ ] Quarterly: Review and update security permissions
- [ ] Annually: Conduct comprehensive report optimization audit
- [ ] Annually: Review and update governance policies

> [!TIP]
> Schedule recurring calendar reminders for these reviews to ensure they don't get overlooked during busy periods.

### Performance Optimization
- [ ] Monitor query performance using Performance Analyzer
- [ ] Identify and optimize slow-running visuals
- [ ] Review and optimize data refresh duration
- [ ] Consider implementing aggregations for large datasets
- [ ] Monitor and manage workspace storage limits
- [ ] Optimize data model size and query efficiency

> [!WARNING]
> Regular performance reviews are essential. Reports that perform well initially can degrade as data volumes grow.

> [!TIP]
> Use aggregations for large fact tables to dramatically improve query performance without changing the user experience.

### Useful Links
- Power BI Service: https://app.powerbi.com
- Power BI Admin Portal: https://app.powerbi.com/admin-portal
- Gateway Documentation: https://docs.microsoft.com/power-bi/connect-data/service-gateway-onprem
- Best Practices: https://docs.microsoft.com/power-bi/guidance/

> [!NOTE]
> This checklist should be customized to fit your organization's specific requirements, policies, and workflows.
