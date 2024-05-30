### Summary Table: Backup Website Infrastructure and Data Usage

| **Component**        | **Instance Type** | **Quantity** | **Capacity per Instance** | **Total Capacity** | **Estimated Data Usage**        |
|----------------------|-------------------|--------------|---------------------------|--------------------|----------------------------------|
| **Frontend**         | T3.medium         | 2            | ~1,578,947 users          | ~3,157,894 users   | N/A (static assets)             |
| **Web Servers**      | M5.large          | 2            | ~239,520 users            | ~479,040 users     | N/A                              |
| **Application Servers** | C5.large       | 2            | ~3,594 users              | ~7,188 users       | 32 KB/user/hour                 |
| **Database**         | R5.large          | 2            | ~11,976 users             | ~23,952 users      | Dependent on user transactions  |
| **Messaging Queue**  | T3.medium         | 2            | ~11,976 users             | ~23,952 users      | 32 KB/user/hour (aggregated)    |
| **Total Data Usage** | -                 | -            | -                         | -                  | 224 MB/hour for 7,188 users     |

### Data Usage Breakdown per Operation

| **Operation**            | **Request Size** | **Response Size** | **Total Size** | **Frequency (per hour)** | **Total Data per User (bytes)** |
|--------------------------|------------------|-------------------|----------------|--------------------------|----------------------------------|
| **Login**                | 279 bytes        | 317 bytes         | 596 bytes      | 1                        | 596                              |
| **Sell Stock**           | 317 bytes        | 417 bytes         | 734 bytes      | 10                       | 7,340                            |
| **View Transaction History** | 224 bytes    | 1,241 bytes       | 1,465 bytes    | 5                        | 7,325                            |
| **Access Portfolio**     | 220 bytes        | 2,265 bytes       | 2,485 bytes    | 5                        | 12,425                           |
| **Live Market Rate**     | -                | -                 | 50 bytes       | 100                      | 5,000                            |
| **Total Data per User per Hour** | -         | -                 | -              | -                        | 32,686 ≈ 32 KB                  |

### Cost Cutting and Optimization Summary

| **Component**              | **Optimization Strategy**                                              |
|----------------------------|-------------------------------------------------------------------------|
| **Frontend**               | Use AWS S3 and CloudFront for static assets, implement caching          |
| **Web Servers**            | Use AWS Auto Scaling, optimize code and database queries                |
| **Application Servers**    | Use AWS Auto Scaling, optimize code and database queries                |
| **Database**               | Use read replicas, implement database caching (Redis/Memcached)        |
| **Messaging Queue**        | Optimize RabbitMQ configuration, use clustering/high availability      |
| **Monitoring and Alerts**  | Use AWS CloudWatch, implement automated failover mechanisms            |

### AWS Infrastructure and Capacity

| **Component**              | **Total Capacity**       | **Notes**                                        |
|----------------------------|--------------------------|--------------------------------------------------|
| **Frontend**               | ~3,157,894 users         | Handled by S3 and CloudFront for static assets   |
| **Web Servers**            | ~479,040 users           | Nginx for web server load balancing              |
| **Application Servers**    | ~7,188 users             | Go for API and services                          |
| **Database**               | ~23,952 users            | PostgreSQL/MySQL with read replicas              |
| **Messaging Queue**        | ~23,952 users            | RabbitMQ for message queuing                     |
| **Total Data Usage**       | 224 MB/hour for 7,188 users | Based on 32 KB/user/hour                         |

This table summarizes the key components, instance types, capacities, data usage, and optimization strategies for the backup website infrastructure.

### Backup Website High-Level Design (HLD) for Order Square-Off

---

### Flow Diagram:

1. **User**
2. **Frontend**
3. **Load Balancer (AWS ELB)**
4. **Web Server (Nginx)**
5. **Load Balancer (AWS ELB)**
6. **Application Server (Go)**
7. **Exchange Adaptor (TCP/IP)**
8. **API Gateway (Go)**
9. **Messaging Queue (RabbitMQ)**
10. **Application Server (Go)**
11. **Database Connected Application Server (Go)**
12. **Existing System Database (PostgreSQL/MySQL)**
13. **BOD Syncing with Database**
14. **Monitoring System**
15. **Control to Backup Application (IRRA Platform)**

---

### Technology Stack:

- **Backend:**
  - API: Go
  - Services: Go
  - Automation/report scripts: Python
  - TAP application: C++
- **Frontend:**
  - ReactJS
- **Databases:**
  - PostgreSQL, MySQL
- **OS:**
  - Linux
- **DevOps & CI/CD:**
  - Docker, Jenkins
- **Developer Tooling:**
  - GitHub
- **Queues and Monitoring:**
  - RabbitMQ
- **Webservers:**
  - Nginx

---

### AWS Infrastructure Components:

1. **Frontend:**
   - **Instance Type:** T3.medium
   - **Quantity:** 2 instances
   - **Capacity per Instance:** ~1,578,947 users (for static asset delivery)
   - **Total Capacity:** ~3,157,894 users

2. **Load Balancers:**
   - **AWS ELB:** Distributes incoming application traffic across multiple targets (web servers, application servers).

3. **Web Servers:**
   - **Instance Type:** M5.large
   - **Quantity:** 2 instances
   - **Capacity per Instance:** ~239,520 users
   - **Total Capacity:** ~479,040 users

4. **Application Servers:**
   - **Instance Type:** C5.large
   - **Quantity:** 2 instances
   - **Capacity per Instance:** ~3,594 users
   - **Total Capacity:** ~7,188 users

5. **Database:**
   - **Instance Type:** R5.large (PostgreSQL/MySQL)
   - **Quantity:** 2 instances
   - **Capacity per Instance:** ~11,976 users
   - **Total Capacity:** ~23,952 users

6. **Messaging Queue (RabbitMQ):**
   - **Instance Type:** T3.medium
   - **Quantity:** 2 instances
   - **Capacity per Instance:** ~11,976 users
   - **Total Capacity:** ~23,952 users

---

### Data Usage Calculation:

1. **Average Request and Response Sizes:**

   - **Login:**
     - Request: ~279 bytes
     - Response: ~317 bytes
     - Total: ~596 bytes

   - **Selling Stock:**
     - Request: ~317 bytes
     - Response: ~417 bytes
     - Total: ~734 bytes

   - **Viewing Transaction History:**
     - Request: ~224 bytes
     - Response: ~1,241 bytes
     - Total: ~1,465 bytes

   - **Accessing Portfolio:**
     - Request: ~220 bytes
     - Response: ~2,265 bytes
     - Total: ~2,485 bytes

   - **Live Market Rate (WebSocket):**
     - Message: ~50 bytes per message

2. **Data Usage per User per Hour:**

   - **Login:**
     - 1 time/hour
     - Data: 596 bytes

   - **Sell Stock:**
     - 10 times/hour
     - Data: 7,340 bytes

   - **View Transaction History:**
     - 5 times/hour
     - Data: 7,325 bytes

   - **Access Portfolio:**
     - 5 times/hour
     - Data: 12,425 bytes

   - **Live Market Rate Updates:**
     - 100 messages/hour
     - Data: 5,000 bytes

   - **Total Data per User per Hour:**
     - 596 + 7,340 + 7,325 + 12,425 + 5,000 = 32,686 bytes ≈ 32 KB

3. **Total Data Usage for All Users:**

   - **Assuming 7,188 users (limited by the Application Server capacity):**
     - Total Data per Hour: 32 KB/user * 7,188 users = 229,376 KB ≈ 224 MB

---

### AWS EC2 Instance Requirements:

#### 1. **Frontend (T3.medium):**

- **Instance Type:** T3.medium
- **Quantity:** 2 instances
- **Capacity per Instance:** ~1,578,947 users
- **Total Capacity:** ~3,157,894 users

#### 2. **Web Servers (M5.large):**

- **Instance Type:** M5.large
- **Quantity:** 2 instances
- **Capacity per Instance:** ~239,520 users
- **Total Capacity:** ~479,040 users

#### 3. **Application Servers (C5.large):**

- **Instance Type:** C5.large
- **Quantity:** 2 instances
- **Capacity per Instance:** ~3,594 users
- **Total Capacity:** ~7,188 users

#### 4. **Database (PostgreSQL/MySQL) (R5.large):**

- **Instance Type:** R5.large
- **Quantity:** 2 instances
- **Capacity per Instance:** ~11,976 users
- **Total Capacity:** ~23,952 users

#### 5. **Messaging Queue (RabbitMQ) (T3.medium):**

- **Instance Type:** T3.medium
- **Quantity:** 2 instances
- **Capacity per Instance:** ~11,976 users
- **Total Capacity:** ~23,952 users

---

### Cost Cutting and Optimization:

1. **Frontend:**
   - Use AWS S3 and CloudFront for serving static assets to reduce the load on EC2 instances.
   - Implement caching strategies.

2. **Web Servers and Application Servers:**
   - Use AWS Auto Scaling to dynamically adjust the number of instances based on traffic.
   - Optimize application code and database queries to reduce CPU and memory usage.

3. **Database:**
   - Use read replicas to offload read queries from the primary database.
   - Implement database caching using Redis or Memcached.

4. **Messaging Queue:**
   - Optimize RabbitMQ configuration to handle peak loads efficiently.
   - Use clustering and high availability configurations.

5. **Monitoring and Alerts:**
   - Use AWS CloudWatch for monitoring and setting up alerts.
   - Implement automated failover mechanisms.

---

### Summary:

By utilizing the given technology stack and AWS infrastructure, the backup website can effectively handle the required user load while ensuring cost efficiency. The optimized instance types and configurations allow for seamless scaling and robust performance, ensuring continuous operations during failover scenarios. The calculated data usage provides a clear understanding of the system's capacity to handle user requests efficiently.

