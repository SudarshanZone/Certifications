### Backup Website for Order Square-Off: Detailed Estimations and AWS Requirements

**Objective:** Ensure seamless order square-off functionalities in the event of a critical failure in the main website infrastructure.

#### 1. Updated Hardware Requirements

**User Base:** 10,88,961 users.
**Peak Simultaneous Users:** 10% of total users (approx. 1,08,896 users).
**Request Size Calculation:**
- **Average Request Size:** 450 bytes (based on detailed request and response headers analysis).
- **Request Rate:** 5 requests per user per minute during peak load.

#### Estimation of Server Requirements

**Web Servers:**
- **Request Rate:** 1,08,896 users * 5 requests/minute = 5,44,480 requests/minute.
- **Requests per Second (RPS):** 5,44,480 / 60 = 9,075 RPS.
- **Web Server Throughput:** Assuming each web server can handle 500 RPS, we would need approximately 19 web servers (9,075 / 500 ≈ 19).

**Application Servers:**
- Similar throughput to web servers.
- Number of servers needed: 19.

**Database Size:**
- **User Data:** Assuming 1 KB per user, total user data = 10,88,961 * 1 KB = ~1 GB.
- **Transaction Data:** Assuming 50 KB per transaction, with 5 transactions/user/day, total transaction data = 10,88,961 users * 50 KB * 5 transactions ≈ 2.5 TB/day.
- **Backup Data Storage:** For a 7-day backup period, total storage required = 7 * 2.5 TB = 17.5 TB.

**Database Servers:**
- For high IOPS and throughput, considering a primary and replica setup:
  - **Primary DB Server:** 1 high-performance instance.
  - **Replica DB Server:** 1 for failover.
  - **Backup Storage:** 20 TB storage.

#### AWS EC2 Instance Recommendations

**Web Servers:**
- **Instance Type:** m5.large (2 vCPU, 8 GB RAM)
- **Number of Instances:** 19

**Application Servers:**
- **Instance Type:** m5.large (2 vCPU, 8 GB RAM)
- **Number of Instances:** 19

**Database Servers:**
- **Primary DB Server:** r5.4xlarge (16 vCPU, 128 GB RAM, EBS-optimized)
- **Replica DB Server:** r5.4xlarge (16 vCPU, 128 GB RAM, EBS-optimized)
- **Backup Storage:** EBS volumes with 20 TB capacity.

**Load Balancers:**
- **Type:** Application Load Balancer (ALB)
- **Number of Load Balancers:** 2 (one for web servers, one for application servers)

**Monitoring and Automation:**
- **CloudWatch:** For monitoring and alerts.
- **Lambda:** For automation scripts to handle failover.
- **SNS:** For notifications.

**Network Configuration:**
- **VPC with Subnets:** Distributed across multiple availability zones for high availability.

#### Cost Estimation

**EC2 Instances (Web and Application Servers):**
- **m5.large:** 
  - **Hourly Cost:** ~$0.096 (on-demand pricing)
  - **Monthly Cost:** 19 instances * 0.096 * 24 * 30 ≈ $13,171.2

**Database Servers:**
- **r5.4xlarge:** 
  - **Hourly Cost:** ~$1.008 (on-demand pricing)
  - **Monthly Cost (Primary + Replica):** 2 * 1.008 * 24 * 30 ≈ $1,451.52

**EBS Storage:**
- **20 TB:** 
  - **Monthly Cost:** $0.10 per GB/month ≈ $2,000

**Load Balancers:**
- **ALB:** 
  - **Monthly Cost:** ~$18.25 (per Load Balancer) + $0.008 per LCU-hour
  - **Monthly Cost Estimation:** 2 ALBs * $18.25 + LCU-hours cost ≈ $100

**Total Monthly Cost:**
- **EC2 Instances:** $13,171.2
- **Database Servers:** $1,451.52
- **EBS Storage:** $2,000
- **Load Balancers:** $100
- **Total:** $16,722.72

#### Table Format Data

| Component            | Instance Type       | Number of Instances | Hourly Cost (per instance) | Monthly Cost (per instance) | Total Monthly Cost |
|----------------------|---------------------|---------------------|----------------------------|-----------------------------|--------------------|
| Web Servers          | m5.large            | 19                  | $0.096                     | $69.12                      | $13,171.20         |
| Application Servers  | m5.large            | 19                  | $0.096                     | $69.12                      | $13,171.20         |
| Primary DB Server    | r5.4xlarge          | 1                   | $1.008                     | $725.76                     | $725.76            |
| Replica DB Server    | r5.4xlarge          | 1                   | $1.008                     | $725.76                     | $725.76            |
| EBS Storage          | -                   | -                   | $0.10 per GB               | -                           | $2,000.00          |
| Load Balancers       | ALB                 | 2                   | $18.25 + $0.008/LCU-hour   | -                           | $100.00            |

**Total Estimated Monthly Cost: $29,893.92**

#### Additional Services

**Monitoring and Automation:**
- **CloudWatch:** For monitoring and alerts.
- **Lambda:** For automation scripts.
- **SNS:** For notifications.

**Network Configuration:**
- **VPC with Subnets:** For high availability.

---

This document provides a detailed estimation of hardware requirements and AWS services needed to ensure business continuity for order square-off functionalities. It considers the peak user load, necessary instance types, storage, and monitoring solutions to deliver a robust and scalable backup website.
