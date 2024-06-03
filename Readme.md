sudarshan
https://www.hackerearth.com/challenges/test/programming-test-cdac-2/?login=583e9a451e6a6c063c04a9fab9536d01



shreyas :-

https://www.hackerearth.com/challenges/test/programming-test-cdac-2/?login=3c585946fee0cd503567cf47d7419ef4








### Estimating Infrastructure Requirements Based on Data Sizes and Load

Given the constraints and requirements, we need to calculate the network, computational, and storage capacities required to handle the requests and responses. This will help us optimize the infrastructure and ensure cost efficiency while meeting the performance requirements.

### Breakdown of Data Sizes

#### Login
- **Request Header Size:** 279 bytes
- **Response Header Size:** 317 bytes
- **Total:** 596 bytes

#### Selling Stock
- **Request Header Size:** 317 bytes
- **Response Header Size:** 417 bytes
- **Total:** 734 bytes

#### Viewing Transaction History
- **Request Header Size:** 224 bytes
- **Response Header Size:** 1241 bytes
- **Total:** 1465 bytes

#### Accessing Portfolio
- **Request Header Size:** 220 bytes
- **Response Header Size:** 2265 bytes
- **Total:** 2485 bytes

#### Live Market Rate (WebSocket)
- **Message Size:** 50 bytes per message

### Calculating Network Bandwidth Requirements

Given:
- Peak users: 550,000
- 1% of peak users: 5,500 concurrent users
- Minimum RPS (Requests Per Second): 2700

#### Estimating Requests Per Second for Each Operation

1. **Login:**
   - Average request rate: 10% of users log in within a minute.
   - Requests per second: \( \frac{5,500 \times 0.1}{60} = 9.17 \approx 10 \) RPS

2. **Selling Stock:**
   - Average request rate: 5% of users perform transactions within a minute.
   - Requests per second: \( \frac{5,500 \times 0.05}{60} = 4.58 \approx 5 \) RPS

3. **Viewing Transaction History:**
   - Average request rate: 2% of users check transaction history within a minute.
   - Requests per second: \( \frac{5,500 \times 0.02}{60} = 1.83 \approx 2 \) RPS

4. **Accessing Portfolio:**
   - Average request rate: 3% of users access their portfolio within a minute.
   - Requests per second: \( \frac{5,500 \times 0.03}{60} = 2.75 \approx 3 \) RPS

5. **Live Market Rate (WebSocket):**
   - Average messages per second: Assume 20 messages per second per user.

### Total Bandwidth Calculation

1. **Login:**
   - Total data per second: \( 10 \times 596 \) bytes = 5960 bytes/sec

2. **Selling Stock:**
   - Total data per second: \( 5 \times 734 \) bytes = 3670 bytes/sec

3. **Viewing Transaction History:**
   - Total data per second: \( 2 \times 1465 \) bytes = 2930 bytes/sec

4. **Accessing Portfolio:**
   - Total data per second: \( 3 \times 2485 \) bytes = 7455 bytes/sec

5. **Live Market Rate (WebSocket):**
   - Total data per second: \( 5,500 \times 20 \times 50 \) bytes = 5,500,000 bytes/sec = 5.5 MB/sec

**Total Network Bandwidth Required:** 
- Login + Selling Stock + Viewing Transaction History + Accessing Portfolio + Live Market Rate
- \( 5960 + 3670 + 2930 + 7455 + 5,500,000 \) bytes/sec
- Total: 5,515,015 bytes/sec = ~5.52 MB/sec

### Optimized Infrastructure Plan

Based on the network requirements and the need to handle peak loads efficiently, here’s the optimized infrastructure plan considering cost-cutting measures:

#### 1. Frontend Web Servers
- **Instance Type:** t3.medium (2 vCPUs, 4 GB RAM)
- **Quantity:** 8 servers (using spot instances for cost savings)
- **Total Capacity:** 8 * 500 = 4000 concurrent users

#### 2. Load Balancers
- **Type:** AWS Application Load Balancer
- **Quantity:** 1
- **Cost per Month:** ₹10,000

#### 3. Application Servers
- **Instance Type:** m5.large (2 vCPUs, 8 GB RAM)
- **Quantity:** 12 servers (using a mix of on-demand and spot instances)
- **Total Capacity:** 12 * 250 = 3000 concurrent sessions

#### 4. Database Servers
- **Type:** AWS RDS for MySQL (db.m5.large)
- **Quantity:** 1 primary, 1 standby
- **Cost per Month:** ₹60,000

#### 5. Messaging Queue
- **Type:** AWS SQS or equivalent
- **Cost per Month:** ₹10,000

#### 6. Monitoring System
- **Type:** AWS CloudWatch or equivalent
- **Cost per Month:** ₹10,000

### Estimated Costs with Optimized Instances

| Component                | Quantity | Cost per Unit (₹) | Total Cost (₹) |
|--------------------------|----------|-------------------|----------------|
| Web Servers (Spot Instances) | 8        | 2,000             | 16,000         |
| Load Balancers           | 1        | 10,000            | 10,000         |
| Application Servers (Mix of On-Demand and Spot Instances) | 12       | 5,000             | 60,000         |
| Database Servers         | 2        | 30,000            | 60,000         |
| Messaging Queue          | -        | -                 | 10,000         |
| Monitoring System        | -        | -                 | 10,000         |

**Total Monthly Cost:** ₹166,000

### Budget Allocation for Additional Capacity

| Category                 | Estimated Cost (₹) |
|--------------------------|--------------------|
| Infrastructure (Servers, DB) | 166,000            |
| Network Costs            | 50,000             |
| Support and Maintenance  | 30,000             |
| Miscellaneous Expenses   | 10,000             |

**Total Monthly Cost:** ₹256,000

### Conclusion

By optimizing the number of servers and leveraging cost-effective cloud solutions such as spot instances and auto-scaling, we can handle the requirements of 2700 RPS and 5,500 concurrent users efficiently. The total cost is kept within the budget of ₹4 lakh per month while ensuring performance and reliability. This configuration also allows for scaling up during peak periods without incurring excessive costs.






s_________----------------#@£_@-#-#-#-#-#-#-£-£-£-£-

### Back-of-the-Envelope Calculations for Hardware Requirements

#### Assumptions:
- **Peak users:** 5.5 lakhs (550,000)
- **1% of peak users during peak duration (half hour):** 0.01 * 550,000 = 5,500 users
- **Peak duration:** Half hour (30 minutes)
- **Average order requests per user:** 2 orders per minute (considering active trading scenario)

#### Components and Their Requirements:

1. **Frontend (Web Servers):**
   - Each web server can handle approximately 500 concurrent users.
   - **Total required web servers:** 
     \[
     \frac{5,500 \text{ users}}{500 \text{ users/server}} = 11 \text{ servers}
     \]

2. **Load Balancer:**
   - To distribute traffic among web servers efficiently.
   - Assume we need 2 load balancers for redundancy.

3. **Application Servers:**
   - Each application server can handle approximately 250 concurrent sessions.
   - **Total required application servers:** 
     \[
     \frac{5,500 \text{ sessions}}{250 \text{ sessions/server}} = 22 \text{ servers}
     \]

4. **Database Servers:**
   - Assume the primary database can handle up to 10,000 read/write operations per second.
   - Considering high availability, we would need at least 2 database servers (Primary and Standby).
   - For peak operations:
     \[
     5,500 \text{ users} \times 2 \text{ orders/min} \times 30 \text{ mins} = 330,000 \text{ transactions}
     \]
   - Transactions per second (TPS):
     \[
     \frac{330,000 \text{ transactions}}{30 \text{ mins} \times 60 \text{ seconds}} = 183 \text{ TPS}
     \]
   - A single database server with 10,000 TPS capability is more than sufficient.

5. **Messaging Queue:**
   - Assume using RabbitMQ/Kafka where each node can handle 50,000 messages per second.
   - For redundancy and load balancing, assume 3 nodes (1 primary and 2 replicas).

6. **Monitoring System:**
   - Assume lightweight monitoring for each component.
   - 1-2 servers dedicated to monitoring tools like Prometheus/Grafana.

#### Summary:

- **Frontend Web Servers:** 11 servers
- **Load Balancers:** 2 units
- **Application Servers:** 22 servers
- **Database Servers:** 2 servers (1 primary, 1 standby)
- **Messaging Queue Nodes:** 3 nodes
- **Monitoring Servers:** 2 servers

#### Estimation of Hardware Specifications:
- **Web Servers:**
  - CPU: 4 cores
  - RAM: 16 GB
  - Storage: 100 GB SSD

- **Application Servers:**
  - CPU: 8 cores
  - RAM: 32 GB
  - Storage: 200 GB SSD

- **Database Servers:**
  - CPU: 16 cores
  - RAM: 64 GB
  - Storage: 1 TB SSD (RAID 10 configuration)

- **Messaging Queue Nodes:**
  - CPU: 8 cores
  - RAM: 32 GB
  - Storage: 500 GB SSD

- **Monitoring Servers:**
  - CPU: 4 cores
  - RAM: 16 GB
  - Storage: 500 GB SSD

### Total Hardware Requirement Summary:

| Component                | Quantity | CPU (Total Cores) | RAM (Total GB) | Storage (Total GB) |
|--------------------------|----------|-------------------|----------------|--------------------|
| Web Servers              | 11       | 44                | 176            | 1100               |
| Load Balancers           | 2        | 8                 | 32             | 200                |
| Application Servers      | 22       | 176               | 704            | 4400               |
| Database Servers         | 2        | 32                | 128            | 2000               |
| Messaging Queue Nodes    | 3        | 24                | 96             | 1500               |
| Monitoring Servers       | 2        | 8                 | 32             | 1000               |

### Conclusion:
This back-of-the-envelope calculation provides an initial estimate of the hardware requirements needed to support the backup website for ICICI Securities' trading platform during failover scenarios. This plan includes redundancy and scalability considerations to ensure continuous operation without performance degradation. Further refinement of these estimates would involve detailed performance testing and profiling based on actual system behavior under load.
