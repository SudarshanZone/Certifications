








# Designing a Backup Website for Order Square-Off to Ensure Business Continuity in Emergency Scenarios

## Problem Statement
In the event of a critical failure in our main website infrastructure, particularly during market hours, it is imperative to ensure seamless order square-off functionalities to prevent financial losses for our users. The proposed backup website, operating on a completely independent technology stack, aims to address this challenge by enabling continuous operations even when the primary website is inaccessible.

## Challenges

1. **Data Synchronization and Integrity:** Maintaining real-time synchronization of trade and order data from the exchange API to the backup website's database while ensuring data integrity. Any discrepancies or delays in data replication could result in inconsistencies in user positions and financial information.

2. **Activation and Switching Mechanism:** Developing a robust mechanism to activate the backup website promptly upon detection of a failure in the main website infrastructure is crucial. This requires efficient monitoring systems capable of swiftly identifying failures and initiating the switch to the backup website without disrupting user access.

3. **User Experience Continuity:** Ensuring a seamless user experience during the transition from the main website to the backup website is essential. Users should be able to log in, view their positions, execute square-off orders, and access financial information without encountering any disruptions or loss of functionality.

4. **Authentication and Security:** Implementing secure authentication mechanisms to protect user accounts and prevent unauthorized access is paramount. Additionally, ensuring secure data transmission between the backup website and the exchange API is crucial to safeguard sensitive financial information.

5. **Performance and Scalability:** The backup website must be capable of handling increased user activity and transaction volume during failover scenarios without sacrificing performance. Scalability measures should be in place to accommodate additional user load without impacting system stability.

## Objectives

1. **Data Preparation and Dumping:** Before the start of each market day, take a dump of the existing database to ensure the backup database is up-to-date.
2. **Backup Activation:** Develop an automated process in the backend to detect main website failures and activate the backup website swiftly without manual intervention.
3. **Real-time Data Synchronization:** Implement mechanisms to continuously receive real-time trade and order data from the exchange API and update the backup website's database.
4. **User Interface Enablement:** Upon activation of the backup website, enable the frontend interface for users to access and perform order square-off operations seamlessly.
5. **User Interaction:** Provide users with login access to the backup website, enabling them to view their positions, execute square-off orders, and access financial information.
6. **Database Update upon Primary System Recovery:** Once the primary website is restored, update the primary database with any changes made to positions or orders on the backup website.
7. **Error Handling and Monitoring:** Implement robust error handling mechanisms and monitoring systems to detect and address any issues that may arise during the backup website's operation.

## Current Scenario
The main website, which is also connected to NSE API and the main database, needs a backup website (IRRA) which will be activated in case the main website fails. The main website is a retail trading and investment service of ICICI Securities, offering services online as well as through a network of branches across India.

## Components Breakdown

### 1. Frontend
- **UI Design:** User interface for login, viewing positions, executing square-off orders, and accessing financial information.
- **Authentication Mechanism:** Secure user authentication using AWS Cognito.

### 2. Web Servers
- **Software:** Nginx
- **Configuration:** Optimized for handling high HTTP request loads.
- **Security Settings:** SSL/TLS encryption, DDoS protection.
- **Horizontal Scaling:** Enabled via AWS Elastic Load Balancing.

### 3. Application Servers
- **Technology:** Node.js
- **Design Pattern:** Microservices architecture
- **Services:** User management, order processing, data synchronization.
- **Communication:** gRPC for microservices communication.
- **Horizontal Scaling:** Enabled via AWS Auto Scaling.

### 4. Database
- **Type:** MySQL (Amazon RDS)
- **Schema Design:** Optimized for trade and order data.
- **Data Synchronization:** Real-time replication with Oracle DB.

### 5. Messaging Queue
- **Technology:** RabbitMQ
- **Queues:** Separate queues for trade data, order processing, and notifications.
- **Configuration:** High availability mode with multiple nodes.

### 6. Monitoring System
- **Tools:** Amazon CloudWatch, Prometheus
- **Metrics:** System performance, user activity, error rates.
- **Alerting Rules:** Configured for system failures, high latency, and data inconsistencies.

## Flow Diagram

1. User
2. Frontend (T3.medium instances)
3. Load Balancer (ALB)
4. Web Server (M5.large instances)
5. Load Balancer (ALB)
6. Application Server (C5.large instances)
7. Exchange Adaptor
8. TCP/IP
9. API Gateway (Amazon API Gateway)
10. Messaging Queue (RabbitMQ on T3.medium instances)
11. Application Server (C5.large instances)
12. Database Connected Application Server (R5.large instances)
13. Existing System Database (Oracle DB)
14. Monitoring System (Amazon CloudWatch and Prometheus)
15. Control to Backup Application (IRRA platform)

## AWS EC2 Instance Requirements

### 1. Frontend
- **Instance Type:** T3.large
- **Total Instances Needed:** 2

### 2. Web Servers
- **Instance Type:** M5.large
- **Total Instances Needed:** 2

### 3. Application Servers
- **Instance Type (Compute-Optimized):** C5.xlarge
- **Total Instances Needed (Compute-Optimized):** 2
- **Instance Type (Memory-Optimized):** R5.xlarge
- **Total Instances Needed (Memory-Optimized):** 2

### 4. Database
- **Instance Type:** db.r5.large (Amazon RDS)
- **Total Instances Needed:** 2 (1 primary, 1 read replica)

### 5. Messaging Queue
- **Instance Type:** T3.large
- **Total Instances Needed:** 2

### 6. Monitoring System
- **Instance Type (General-Purpose):** T3.large
- **Total Instances Needed (General-Purpose):** 1
- **Instance Type (Memory-Optimized):** R5.large
- **Total Instances Needed (Memory-Optimized):** 1

## Summary of AWS EC2 Instances

| Component          | Instance Type   | Total Instances Needed |
|--------------------|-----------------|------------------------|
| Frontend           | T3.large        | 2                      |
| Web Servers        | M5.large        | 2                      |
| Application Servers (Compute-Optimized) | C5.xlarge       | 2                      |
| Application Servers (Memory-Optimized)  | R5.xlarge       | 2                      |
| Database           | db.r5.large     | 2                      |
| Messaging Queue    | T3.large        | 2                      |
| Monitoring System (General-Purpose) | T3.large  | 1                      |
| Monitoring System (Memory-Optimized) | R5.large  | 1                      |

This setup reduces the total number of instances while still ensuring high availability, scalability, and redundancy to accommodate user activity during failover scenarios. Adjustments may be required based on actual load testing and performance monitoring.

## Conclusion
The proposed backup website infrastructure, using the specified AWS EC2 instances and configurations, will ensure continuous operations during main website failures, providing a seamless user experience and preventing financial losses. This optimized setup balances cost-effectiveness with performance and scalability to meet the business continuity requirements.
