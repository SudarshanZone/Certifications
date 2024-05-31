### Optimized Backup Website for Order Square-Off: Detailed Estimations and AWS Requirements

**Objective:** Ensure seamless order square-off functionalities in the event of a critical failure in the main website infrastructure with cost-effective AWS instance usage.

#### User Base Estimation

**Total Users:** 10,88,961
**Peak Simultaneous Users:** 10% of total users (approx. 1,08,896 users)
**Request Size Calculation:**
- **Average Request Size:** 450 bytes (based on detailed request and response headers analysis)
- **Request Rate:** 5 requests per user per minute during peak load

#### Optimized Estimation of Server Requirements

**Web Servers:**
- **Request Rate:** 1,08,896 users * 5 requests/minute = 5,44,480 requests/minute
- **Requests per Second (RPS):** 5,44,480 / 60 = 9,075 RPS
- **Web Server Throughput:** Assuming each web server can handle 1,000 RPS, we would need approximately 10 web servers (9,075 / 1,000 ≈ 10)

**Application Servers:**
- Similar throughput to web servers
- Number of servers needed: 10

**Database Size:**
- **User Data:** Assuming 1 KB per user, total user data = 10,88,961 * 1 KB = ~1 GB
- **Transaction Data:** Assuming 50 KB per transaction, with 5 transactions/user/day, total transaction data = 10,88,961 users * 50 KB * 5 transactions ≈ 2.5 TB/day
- **Backup Data Storage:** For a 7-day backup period, total storage required = 7 * 2.5 TB = 17.5 TB

**Database Servers:**
- For high IOPS and throughput, considering a primary and replica setup:
  - **Primary DB Server:** 1 high-performance instance
  - **Replica DB Server:** 1 for failover
  - **Backup Storage:** 20 TB storage

#### AWS EC2 Instance Recommendations

**Web Servers:**
- **Instance Type:** t3.large (2 vCPU, 8 GB RAM)
- **Number of Instances:** 10
- **Users Handled per Instance:** 10,000 RPS / 1,000 RPS per instance = 10,000 users per instance
- **Total Users Handled:** 10 instances * 10,000 users = 100,000 users

**Application Servers:**
- **Instance Type:** t3.large (2 vCPU, 8 GB RAM)
- **Number of Instances:** 10
- **Users Handled per Instance:** 10,000 users
- **Total Users Handled:** 10 instances * 10,000 users = 100,000 users

**Database Servers:**
- **Primary DB Server:** r5.2xlarge (8 vCPU, 64 GB RAM, EBS-optimized)
- **Replica DB Server:** r5.2xlarge (8 vCPU, 64 GB RAM, EBS-optimized)
- **Backup Storage:** EBS volumes with 20 TB capacity
- **Users Handled per DB Server:** Supports up to 1,08,896 users (since these are high-performance instances and designed to handle significant load)

**Load Balancers:**
- **Type:** Application Load Balancer (ALB)
- **Number of Load Balancers:** 2 (one for web servers, one for application servers)

**Monitoring and Automation:**
- **CloudWatch:** For monitoring and alerts
- **Lambda:** For automation scripts to handle failover
- **SNS:** For notifications

**Network Configuration:**
- **VPC with Subnets:** Distributed across multiple availability zones for high availability

#### Cost Estimation in INR

**Exchange Rate:** 1 USD = 80 INR (approx.)

**EC2 Instances (Web and Application Servers):**
- **t3.large:** 
  - **Hourly Cost:** ~$0.0832 (on-demand pricing)
  - **Monthly Cost (USD):** 10 instances * 0.0832 * 24 * 30 ≈ $598.56
  - **Monthly Cost (INR):** 598.56 * 80 ≈ ₹47,884.80

**Database Servers:**
- **r5.2xlarge:** 
  - **Hourly Cost:** ~$0.504 (on-demand pricing)
  - **Monthly Cost (USD):** 2 * 0.504 * 24 * 30 ≈ $725.76
  - **Monthly Cost (INR):** 725.76 * 80 ≈ ₹58,060.80

**EBS Storage:**
- **20 TB:** 
  - **Monthly Cost (USD):** $0.10 per GB ≈ $2,000
  - **Monthly Cost (INR):** 2,000 * 80 ≈ ₹1,60,000

**Load Balancers:**
- **ALB:** 
  - **Monthly Cost (USD):** ~$18.25 (per Load Balancer) + $0.008 per LCU-hour
  - **Monthly Cost Estimation (USD):** 2 ALBs * $18.25 + LCU-hours cost ≈ $100
  - **Monthly Cost (INR):** 100 * 80 ≈ ₹8,000

**Total Monthly Cost (INR):**
- **EC2 Instances (Web and App Servers):** ₹47,884.80
- **Database Servers:** ₹58,060.80
- **EBS Storage:** ₹1,60,000
- **Load Balancers:** ₹8,000
- **Total:** ₹2,73,945.60

#### Table Format Data

| Component            | Instance Type       | Number of Instances | Hourly Cost (USD) | Monthly Cost (USD) | Monthly Cost (INR) | Users Handled per Instance | Total Users Handled |
|----------------------|---------------------|---------------------|-------------------|---------------------|--------------------|----------------------------|---------------------|
| Web Servers          | t3.large            | 10                  | $0.0832           | $598.56             | ₹47,884.80         | 10,000                     | 100,000             |
| Application Servers  | t3.large            | 10                  | $0.0832           | $598.56             | ₹47,884.80         | 10,000                     | 100,000             |
| Primary DB Server    | r5.2xlarge          | 1                   | $0.504            | $362.88             | ₹29,030.40         | 1,08,896                   | 1,08,896            |
| Replica DB Server    | r5.2xlarge          | 1                   | $0.504            | $362.88             | ₹29,030.40         | 1,08,896                   | 1,08,896            |
| EBS Storage          | -                   | -                   | -                 | $2,000              | ₹1,60,000          | -                          | -                   |
| Load Balancers       | ALB                 | 2                   | $18.25 + LCU cost | $100                | ₹8,000             | -                          | -                   |

**Total Estimated Monthly Cost:** ₹2,73,945.60

#### Additional Services

**Monitoring and Automation:**
- **CloudWatch:** For monitoring and alerts
- **Lambda:** For automation scripts
- **SNS:** For notifications

**Network Configuration:**
- **VPC with Subnets:** For high availability

---

This document provides a cost-optimized estimation of hardware requirements and AWS services needed to ensure business continuity for order square-off functionalities. It balances performance and cost by using appropriate instance types and provides a detailed cost estimation in INR, along with the user handling capacity for each component.
