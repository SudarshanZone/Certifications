## Filling out the Server Requirements Form (Estimates)

**Important Note:** Due to the absence of specific load testing data, these are high-level estimates. Conduct load testing to determine the most accurate server requirements. 

**Filling Instructions:**

1. Copy the table provided in the email.
2. Fill out each section for each component (Application Tier, Web Server, Database) based on the following information and estimates.

**Component | Count of Servers Required | Details**
---|---|---|
**Application Tier (Go API & Services)** | 3 (Initial Estimate) |  * OS: Linux  * Environment: Prod  * Type: Virtual  * Server Role: App  * Cores per VM: 4 (baseline)  * Memory per VM: 16 GB (baseline)  * Application ASLC Status: To be determined  * Zone: DMZ  * Location: To be determined  * Type of App: Go  *  We've estimated 3 servers initially, but this can be adjusted based on load testing. Autoscaling can be implemented to automatically adjust instances based on traffic.
**Web Server (Nginx)** | 2 (Initial Estimate) | * OS: Linux  * Environment: Prod  * Type: Virtual  * Server Role: Web  * Cores per VM: 2 (baseline)  * Memory per VM: 8 GB (baseline)  * Application ASLC Status: To be determined  * Zone: DMZ  * Location: To be determined  * Type of Web: Nginx  * Internet Facing: Yes  * DNS Name: To be determined  * SSL Required: Yes  * Load Balancing Required: Yes  * We've estimated 2 servers initially with a load balancer to distribute traffic. This can be adjusted based on load testing.
**Database (PostgreSQL or MySQL)** | 1 (Initial Estimate) | * OS: Linux  * Environment: Prod  * Type: Virtual  * Server Role: DB  * Cores per VM: 8 (baseline)  * Memory per VM: 32 GB (baseline)  * Application ASLC Status: To be determined  * Zone: DMZ  * Location: To be determined  * Type of DB: PostgreSQL (or MySQL)  * DB Version: To be determined  * DB Name: To be determined  * Character Set: To be determined  * We've estimated 1 database server initially. Scaling storage up can be done if needed.

**Additional Notes:**

*  **Application CAN ID:**  This information might be specific to your organization's internal tracking system. Fill it out accordingly.
* **Storage:** We haven't included specific storage estimations.  Consider your current storage usage and projected growth to determine storage requirements for each component.
* **Mount Points:** If applicable, specify the mount points for your application and database storage.
* **Cost Optimization:** Consider using AWS Reserved Instances, Savings Plans, and Auto Scaling to optimize costs.

**Remember:** These are initial estimates. Conduct load testing to determine the most accurate server requirements for your specific use case.
