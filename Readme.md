To estimate the number of users an EC2 instance type can handle based on its features, we can use the following formulas:

1. **Network Bandwidth Calculation:**
   - **Total Bandwidth (Mbps)** = Network Bandwidth (Gbps) * 1000
   - **Requests per Second Supported by Bandwidth** = Total Bandwidth / Average Request Size (in MB) * 1024 (to convert MB to KB)
   - **Estimated Users Based on Network Capacity** = Requests per Second Supported by Bandwidth / Requests per Second per User

2. **CPU Capacity Calculation:**
   - **Total Requests per Second Supported by CPU** = Number of vCPUs * Requests per Second per Core
   - **Estimated Users Based on CPU Capacity** = Total Requests per Second Supported by CPU / Requests per Second per User

3. **Memory Capacity Calculation:**
   - **Total Memory Available (MiB)** = Memory (GiB) * 1024
   - **Estimated Users Based on Memory Capacity** = Total Memory Available / Memory Usage per User (in MiB)

4. **Overall Estimate:**
   - **Estimated Users** = Minimum of the estimates from network, CPU, and memory capacity calculations

Where:
- **Network Bandwidth (Gbps)**: Maximum network bandwidth provided by the EC2 instance type in gigabits per second (Gbps).
- **Average Request Size**: Average size of a request in megabytes (MB).
- **Requests per Second per User**: Number of requests a single user generates per second.
- **Number of vCPUs**: Number of virtual CPUs provided by the EC2 instance type.
- **Requests per Second per Core**: Number of requests a single CPU core can handle per second.
- **Memory (GiB)**: Total memory available on the EC2 instance type in gibibytes (GiB).
- **Memory Usage per User**: Amount of memory consumed by each user in MiB.

These formulas provide an approximate estimate and may vary based on various factors such as application workload, system configurations, and user behavior. Adjustments may be necessary based on specific use cases and performance testing.





%%%%%%%%%%%%%%%%%©©©©©©©©©©©©©©©©©©©

Given the detailed breakdown of request and response sizes, we need to update the calculations for the number of users each component can handle. This will allow us to determine the minimum number of instances required while ensuring cost-effectiveness and scalability.

### Updated Assumptions and Metrics:
1. **Average Request Sizes:**
   - Login Request: 279 bytes
   - Login Response: 317 bytes
   - Selling Stock Request: 317 bytes
   - Selling Stock Response: 417 bytes
   - Viewing Transaction History Request: 224 bytes
   - Viewing Transaction History Response: 1241 bytes
   - Accessing Portfolio Request: 220 bytes
   - Accessing Portfolio Response: 2265 bytes
   - Average request size (simplified): 450 bytes
   - Average response size (simplified): 500 bytes

2. **Frontend Requests:**
   - Average requests per user: 10 per minute
   - Total request rate: 10 requests/user/min * (450 bytes/request + 500 bytes/response) = 9500 bytes/user/min ≈ 9.5 KB/user/min

3. **Web Servers (Nginx) Handling:**
   - Nginx can handle around 10,000 requests per second (rps) on a large instance.
   - Each M5.large instance: 10,000 rps
   - We have 2 M5.large instances: 2 * 10,000 rps = 20,000 rps

4. **Application Servers (Node.js):**
   - Average requests per second: 300 rps for a C5.xlarge instance (realistic load, considering business logic complexity)
   - We have 2 C5.xlarge instances: 2 * 300 rps = 600 rps

5. **Database (MySQL on RDS):**
   - R5.large can handle around 1,000 transactions per second (tps)
   - We have 2 R5.large instances: 2 * 1,000 tps = 2,000 tps

6. **Messaging Queue (RabbitMQ):**
   - A T3.large instance can handle around 1,000 messages per second.
   - We have 2 T3.large instances: 2 * 1,000 = 2,000 messages per second

### Calculation of User Capacity

#### 1. **Frontend Handling Capacity**
- Total data throughput capacity: Each T3.large instance has a network bandwidth of up to 5 Gbps.
- Total throughput for 2 instances: 2 * 5 Gbps = 10 Gbps
- Converting to MBps: 10 Gbps * 125 = 1250 MBps
- Average data per user per minute: 9.5 KB/user/min ≈ 0.0095 MB/user/min
- Users supported: 1250 MBps / (0.0095 MB/user/min / 60 seconds) ≈ 7,894,737 users

#### 2. **Web Server Handling Capacity**
- Total request handling capacity: 20,000 rps
- Average requests per user per second: 10 requests/user/min / 60 seconds ≈ 0.167 rps/user
- Users supported: 20,000 rps / 0.167 rps/user ≈ 119,760 users

#### 3. **Application Server Handling Capacity**
- Total request handling capacity: 600 rps
- Average requests per user per second: 0.167 rps/user
- Users supported: 600 rps / 0.167 rps/user ≈ 3,593 users

#### 4. **Database Handling Capacity**
- Total transaction handling capacity: 2,000 tps
- Average transactions per user per second: Assuming 1 transaction per request = 0.167 tps/user
- Users supported: 2,000 tps / 0.167 tps/user ≈ 11,976 users

#### 5. **Messaging Queue Handling Capacity**
- Total message handling capacity: 2,000 messages per second
- Average messages per user per second: Assuming 1 message per request = 0.167 mps/user
- Users supported: 2,000 mps / 0.167 mps/user ≈ 11,976 users

### Bottleneck Analysis and Final Estimation:
- The application servers (C5.xlarge) are still the primary bottleneck in this setup, supporting approximately 3,593 users.
- Database and messaging queue capacities are higher but should be monitored as load increases.

### AWS EC2 Instance Requirements Summary:

| Component             | Instance Type | Total Instances | Users Supported per Instance | Total Users Supported |
|-----------------------|---------------|-----------------|------------------------------|-----------------------|
| Frontend              | T3.large      | 2               | 3,947,368                    | 7,894,737             |
| Web Servers           | M5.large      | 2               | 59,880                       | 119,760               |
| Application Servers   | C5.xlarge     | 2               | 1,796.5                      | 3,593                 |
| Database (MySQL RDS)  | R5.large      | 2               | 5,988                        | 11,976                |
| Messaging Queue       | T3.large      | 2               | 5,988                        | 11,976                |

### Conclusion:
The proposed backup website infrastructure can handle approximately 3,593 users simultaneously, with the application servers (C5.xlarge) being the limiting factor. This setup ensures business continuity with minimized infrastructure while providing a robust solution for emergency scenarios.

**Note:** These calculations are based on average load metrics and may vary with actual user behavior. It's recommended to perform load testing to fine-tune the infrastructure requirements further.

---

### Document Representation

---

**Designing a Backup Website for Order Square-Off to Ensure Business Continuity in Emergency Scenarios**

**Problem Statement:**
In the event of a critical failure in our main website infrastructure during market hours, we must ensure seamless order square-off functionalities to prevent financial losses for our users. The proposed backup website, operating on an independent technology stack, aims to address this challenge by enabling continuous operations even when the primary website is inaccessible.

**Challenges:**
1. Data Synchronization and Integrity
2. Activation and Switching Mechanism
3. User Experience Continuity
4. Authentication and Security
5. Performance and Scalability

**Objectives:**
1. Data Preparation and Dumping
2. Backup Activation
3. Real-time Data Synchronization
4. User Interface Enablement
5. User Interaction
6. Database Update upon Primary System Recovery
7. Error Handling and Monitoring

**Current Scenario:**
The main website is connected to the NSE API and the main database. We are developing a backup website to be activated if the main website fails. This backup website will handle the core functionalities:
1. Square Off - Close Open Positions
2. View Positions
3. Access Financial Information
4. Login Authentication and Profile Management

**Infrastructure Requirements and Estimation:**

| Component             | Instance Type | Total Instances | Users Supported per Instance | Total Users Supported |
|-----------------------|---------------|-----------------|------------------------------|-----------------------|
| Frontend              | T3.large      | 2               | 3,947,368                    | 7,894,737             |
| Web Servers           | M5.large      | 2               | 59,880                       | 119,760               |
| Application Servers   | C5.xlarge     | 2               | 1,796.5                      | 3,593                 |
| Database (MySQL RDS)  | R5.large      | 2               | 5,988                        | 11,976                |
| Messaging Queue       | T3.large      | 2               | 5,988                        | 11,976                |

### User Handling Calculation:

#### Average User Load:
- Request size: 450 bytes
- Response size: 500 bytes
- Total data per user per minute: 9500 bytes (≈ 9.5 KB)
- Average requests per user per minute: 10

#### Capacity Analysis:
- **Frontend:** Can support up to 7,894,737 users.
- **Web Servers:** Can support up to 119,760 users.
- **Application Servers:** Can support up to 3,593 users.
- **Database:** Can support up to 11,976 users.
- **Messaging Queue:** Can support up to 11,976 users.

**Conclusion:**
The backup website infrastructure can handle approximately 3,593 users simultaneously. This setup ensures business continuity with minimized infrastructure while providing a robust solution for emergency scenarios.



.




_____________________________________

Sure, let's estimate how many users the proposed AWS setup can handle and provide the calculations for it. We need to consider the capabilities of each component (frontend, web servers, application servers, database, and messaging queue) and the expected load per user.

### Assumptions and Metrics:
1. **Frontend Requests:**
   - Average requests per user: 10 per minute
   - Average request size: 100 KB
   - Total request rate: 10 requests/user/min * 100 KB/request = 1 MB/user/min

2. **Web Servers (Nginx) Handling:**
   - Nginx can handle around 10,000 requests per second (rps) on a large instance.
   - Each M5.large instance: 10,000 rps
   - We have 2 M5.large instances: 2 * 10,000 rps = 20,000 rps

3. **Application Servers (Node.js):**
   - Average requests per second: 300 rps for a C5.xlarge instance (realistic load, considering business logic complexity)
   - We have 2 C5.xlarge instances: 2 * 300 rps = 600 rps

4. **Database (MySQL on RDS):**
   - R5.large can handle around 1,000 transactions per second (tps)
   - We have 2 R5.large instances: 2 * 1,000 tps = 2,000 tps

5. **Messaging Queue (RabbitMQ):**
   - A T3.large instance can handle around 1,000 messages per second.
   - We have 2 T3.large instances: 2 * 1,000 = 2,000 messages per second

### Calculation of User Capacity

#### 1. **Frontend Handling Capacity**
- Total data throughput capacity: Each T3.large instance has a network bandwidth of up to 5 Gbps.
- Total throughput for 2 instances: 2 * 5 Gbps = 10 Gbps
- Converting to MBps: 10 Gbps * 125 = 1250 MBps
- Average data per user per minute: 1 MB/user/min
- Users supported: 1250 MBps / (1 MB/user/min / 60 seconds) = 75,000 users

#### 2. **Web Server Handling Capacity**
- Total request handling capacity: 20,000 rps
- Average requests per user per second: 10 requests/user/min / 60 seconds = 0.167 rps/user
- Users supported: 20,000 rps / 0.167 rps/user = 119,760 users

#### 3. **Application Server Handling Capacity**
- Total request handling capacity: 600 rps
- Average requests per user per second: 0.167 rps/user
- Users supported: 600 rps / 0.167 rps/user = 3,593 users

#### 4. **Database Handling Capacity**
- Total transaction handling capacity: 2,000 tps
- Average transactions per user per second: Assuming 1 transaction per request = 0.167 tps/user
- Users supported: 2,000 tps / 0.167 tps/user = 11,976 users

#### 5. **Messaging Queue Handling Capacity**
- Total message handling capacity: 2,000 messages per second
- Average messages per user per second: Assuming 1 message per request = 0.167 mps/user
- Users supported: 2,000 mps / 0.167 mps/user = 11,976 users

      | 2 
