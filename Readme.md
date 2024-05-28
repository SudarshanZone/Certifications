# Backup Website Hardware Requirements for 1.86 Million Users on AWS

## Overview

To ensure seamless operation and high availability of our backup website in case of a failure of the main website, we need to estimate the hardware requirements based on 1.86 million active users. The following document provides a detailed estimation for each critical component, utilizing AWS services such as EC2 instances, RDS for databases, and other relevant AWS offerings.

## AWS Services and Configurations

### 1. Frontend Servers (Web Servers)

**Assumptions**:
- Peak concurrent users: 5% of total active users (93,108 users)
- Each web server can handle 2,000 concurrent connections

**Calculation**:
- Number of web servers needed = Peak concurrent users / Connections per server
- = 93,108 / 2,000
- = 47 servers

**Configuration**:
- **AWS Service**: EC2
- **Instance Type**: m5.large (2 vCPUs, 8GB RAM)
- **Quantity**: 47

### 2. Application Servers

**Assumptions**:
- Each application server can handle 1,000 RPS (requests per second)
- Average of 5 requests per second per active user during peak hours (93,108 users * 5 RPS = 465,540 RPS)

**Calculation**:
- Number of application servers needed = Total RPS / RPS per server
- = 465,540 / 1,000
- = 466 servers

**Configuration**:
- **AWS Service**: EC2
- **Instance Type**: m5.2xlarge (8 vCPUs, 32GB RAM)
- **Quantity**: 466

### 3. Database Servers

**Assumptions**:
- High volume of transactions requires robust and scalable database setup
- Utilize sharding and replication for performance and redundancy
- Assume 1 primary server for write operations and 2 replicas for read operations

**Calculation**:
- Number of shards based on user data distribution: Assume 1 shard per 200,000 users
- Total shards = 1,862,161 / 200,000
- = 10 shards
- Each shard has 1 primary and 2 replica servers = 10 * 3
- = 30 servers

**Configuration**:
- **AWS Service**: RDS for MySQL/PostgreSQL
- **Instance Type**: db.m5.4xlarge (16 vCPUs, 64GB RAM, 1TB SSD storage)
- **Quantity**: 30 (10 primary, 20 replicas)

### 4. Messaging Queue Servers

**Assumptions**:
- Each server can handle 50,000 messages per second
- Peak messaging load: 465,540 RPS

**Calculation**:
- Number of messaging queue servers needed = Peak RPS / Messages per server
- = 465,540 / 50,000
- = 10 servers

**Configuration**:
- **AWS Service**: EC2 with Kafka or Amazon MSK (Managed Streaming for Kafka)
- **Instance Type**: m5.large (2 vCPUs, 8GB RAM)
- **Quantity**: 10

### 5. Load Balancers

**Assumptions**:
- Load balancers should handle twice the peak concurrent connections for redundancy

**Calculation**:
- Number of load balancers needed = (Peak concurrent users / Connections per load balancer) * 2
- Assuming each load balancer can handle 50,000 connections
- = (93,108 / 50,000) * 2
- = 4 load balancers

**Configuration**:
- **AWS Service**: Elastic Load Balancing (ELB)
- **Quantity**: 4

### 6. Monitoring and Alerting System

**Assumptions**:
- Monitoring infrastructure should be able to collect metrics from all servers and handle alerting

**Calculation**:
- Based on 466 application servers, 47 web servers, 30 database servers, and 10 messaging queue servers (total 553 servers)
- Assume 1 monitoring server per 100 monitored servers

**Configuration**:
- **AWS Service**: CloudWatch
- **Instance Type**: m5.large (2 vCPUs, 8GB RAM)
- **Quantity**: 6 (rounding up)

### 7. Backup Infrastructure

**Assumptions**:
- Ensure data redundancy and backup for databases and critical services

**Calculation**:
- Based on the number of database servers (30), assume 1 backup server for every 5 database servers

**Configuration**:
- **AWS Service**: S3 for backups, EC2 for backup servers
- **Instance Type**: m5.large (2 vCPUs, 8GB RAM)
- **Quantity**: 6 (rounding up)

## Summary of Hardware Requirements

| Component               | Quantity | AWS Service                     | Instance Type            | Configuration                |
|-------------------------|----------|---------------------------------|--------------------------|------------------------------|
| **Web Servers**         | 47       | EC2                             | m5.large                 | 2 vCPUs, 8GB RAM each        |
| **Application Servers** | 466      | EC2                             | m5.2xlarge               | 8 vCPUs, 32GB RAM each       |
| **Database Servers**    | 30       | RDS (MySQL/PostgreSQL)          | db.m5.4xlarge            | 16 vCPUs, 64GB RAM, 1TB SSD  |
| **Messaging Queue**     | 10       | EC2 / Amazon MSK                | m5.large                 | 2 vCPUs, 8GB RAM each        |
| **Load Balancers**      | 4        | Elastic Load Balancing (ELB)    | N/A                      | N/A                          |
| **Monitoring Servers**  | 6        | EC2 / CloudWatch                | m5.large                 | 2 vCPUs, 8GB RAM each        |
| **Backup Servers**      | 6        | EC2 / S3 for storage            | m5.large                 | 2 vCPUs, 8GB RAM each        |

## Additional Considerations

- **Network Bandwidth**: Ensure sufficient bandwidth to handle peak traffic loads, especially for real-time data synchronization.
- **Redundancy and Failover**: Implement redundant network paths and failover mechanisms to ensure high availability.
- **Scalability**: Design the system to scale horizontally, allowing the addition of more servers as needed.

These estimates provide a starting point for planning and budgeting the backup website infrastructure to handle 1.86 million active users using AWS services. Adjustments may be necessary based on detailed performance testing and specific business requirements.
