### Server Requirements Document

#### Problem Statement Summary

In the event of a critical failure in our main website infrastructure, particularly during market hours, it is imperative to ensure seamless order square-off functionalities to prevent financial losses for our users. The proposed backup website, operating on a completely independent technology stack, aims to address this challenge by enabling continuous operations even when the primary website is inaccessible.

### Server Requirements Estimation

Based on the provided traffic, storage, bandwidth, and memory requirements, here's the server requirement estimation for each component of the backup application server. The details are focused on achieving high efficiency and availability.

#### 1. **Application Servers**

| Field                               | Value                                                |
|-------------------------------------|------------------------------------------------------|
| Count of Servers required           | 40                                                   |
| Application Name                    | Backup Application Server                            |
| Application CAN ID                  | CAN12345                                             |
| Application Tier                    | Backend                                              |
| Application ASLC Status             | Active                                               |
| Zone (CORP/DMZ)                     | CORP                                                 |
| Location (HYD/JPR)                  | HYD                                                  |
| OS (WINDOWS/LINUX/AIX)              | LINUX                                                |
| OS Version                          | Ubuntu 22.04 LTS (latest stable version)             |
| Environment (Prod/FallBack/UAT/Dev) | Prod, Fallback                                       |
| Type (Physical/Virtual)             | Virtual                                              |
| Server Role (App/Web/DB)            | App                                                  |
| Core (CPU) Per VM                   | 8                                                    |
| Memory (RAM) Per VM                 | 32 GB                                                |
| App/DB Storage in GB with Mount Points | 200 GB (App: /var/www/, Logs: /var/log/)         |
| Type of App in case of Role is App  | Go                                                   |
| Type of Web in case of Role is Web  | N/A                                                  |
| Type of DB in case of Role is DB    | N/A                                                  |
| Internet Facing (Yes/No)            | No                                                   |
| DNS Name                            | backup-app-server.domain.com                         |
| SSL Required (Yes/No)               | Yes                                                  |
| Load Balancing Require (Yes/No)     | Yes                                                  |

#### 2. **Database Servers**

| Field                               | Value                                                |
|-------------------------------------|------------------------------------------------------|
| Count of Servers required           | 5                                                    |
| Application Name                    | Backup Database Server                               |
| Application CAN ID                  | CAN54321                                             |
| Application Tier                    | Backend                                              |
| Application ASLC Status             | Active                                               |
| Zone (CORP/DMZ)                     | CORP                                                 |
| Location (HYD/JPR)                  | HYD                                                  |
| OS (WINDOWS/LINUX/AIX)              | LINUX                                                |
| OS Version                          | Ubuntu 22.04 LTS (latest stable version)             |
| Environment (Prod/FallBack/UAT/Dev) | Prod, Fallback                                       |
| Type (Physical/Virtual)             | Virtual                                              |
| Server Role (App/Web/DB)            | DB                                                   |
| Core (CPU) Per VM                   | 16                                                   |
| Memory (RAM) Per VM                 | 64 GB                                                |
| App/DB Storage in GB with Mount Points | 60 TB (Data: /var/lib/mysql/, Logs: /var/log/)   |
| Type of App in case of Role is App  | N/A                                                  |
| Type of Web in case of Role is Web  | N/A                                                  |
| Type of DB in case of Role is DB    | MySQL                                                |
| Internet Facing (Yes/No)            | No                                                   |
| DNS Name                            | backup-db-server.domain.com                          |
| SSL Required (Yes/No)               | Yes                                                  |
| Load Balancing Require (Yes/No)     | Yes                                                  |
| DB Version (Oracle/SQL Version)     | MySQL 8.0                                            |
| DB Name                             | backupdb                                             |
| Character Set                       | UTF8MB4                                              |

#### 3. **Web Servers**

| Field                               | Value                                                |
|-------------------------------------|------------------------------------------------------|
| Count of Servers required           | 5                                                    |
| Application Name                    | Backup Web Server                                    |
| Application CAN ID                  | CAN67890                                             |
| Application Tier                    | Frontend                                             |
| Application ASLC Status             | Active                                               |
| Zone (CORP/DMZ)                     | DMZ                                                  |
| Location (HYD/JPR)                  | HYD                                                  |
| OS (WINDOWS/LINUX/AIX)              | LINUX                                                |
| OS Version                          | Ubuntu 22.04 LTS (latest stable version)             |
| Environment (Prod/FallBack/UAT/Dev) | Prod, Fallback                                       |
| Type (Physical/Virtual)             | Virtual                                              |
| Server Role (App/Web/DB)            | Web                                                  |
| Core (CPU) Per VM                   | 8                                                    |
| Memory (RAM) Per VM                 | 16 GB                                                |
| App/DB Storage in GB with Mount Points | 200 GB (App: /var/www/, Logs: /var/log/)         |
| Type of App in case of Role is App  | N/A                                                  |
| Type of Web in case of Role is Web  | Apache                                               |
| Type of DB in case of Role is DB    | N/A                                                  |
| Internet Facing (Yes/No)            | Yes                                                  |
| DNS Name                            | backup-web-server.domain.com                         |
| SSL Required (Yes/No)               | Yes                                                  |
| Load Balancing Require (Yes/No)     | Yes                                                  |

### Summary of Server Requirements

1. **Application Servers:**
   - **Count:** 40
   - **OS:** Linux (Ubuntu 22.04 LTS)
   - **Specs:** 8 CPU cores, 32 GB RAM, 200 GB storage
   - **Role:** Application server for core functionalities (Go application)

2. **Database Servers:**
   - **Count:** 5
   - **OS:** Linux (Ubuntu 22.04 LTS)
   - **Specs:** 16 CPU cores, 64 GB RAM, 60 TB storage (with 3x replication)
   - **Role:** Database server (MySQL 8.0)

3. **Web Servers:**
   - **Count:** 5
   - **OS:** Linux (Ubuntu 22.04 LTS)
   - **Specs:** 8 CPU cores, 16 GB RAM, 200 GB storage
   - **Role:** Web server (Apache)

### Deployment Zones and Locations

- **Zones:** CORP for application and database servers, DMZ for web servers
- **Locations:** HYD (Hyderabad)

### Environment

- **Environments Covered:** Production and Fallback

### Additional Requirements

- **SSL:** Required for all servers
- **Load Balancing:** Required for all roles
- **Monitoring:** Use Grafana for application and database servers
- **Messaging Queue:** Use RabbitMQ for data synchronization

### Traffic Calculation Summary

- **Daily Active Users:** 10 million
- **Write Requests per Day:** 1 million
- **Read Requests per Day:** 50 million
- **Read Requests per Second:** 500
- **Write Requests per Second:** 10
- **New Data per Day:** 10 GB
- **Retention Period:** 5 years
- **Total Storage Required:** 60 TB (with 3x replication)
- **Incoming Data Bandwidth:** 100 KB/s
- **Outgoing Data Bandwidth:** 5 MB/s
- **Memory for Caching:** 70 GB

### Memory and CPU Calculation for Application Servers

- **Requests per Second to Handle:** 500
- **Cores Required per Server:** 8
- **Memory Required per Server:** 32 GB
- **Number of Servers Required:** 40

### Full Document

This document outlines the server requirements, traffic calculations, storage needs, and the necessary environment for setting up a backup application server to ensure business continuity during critical failures of the main website infrastructure. The calculated specifications are designed to handle the projected traffic and data load efficiently, ensuring seamless operation and minimal disruption to users.
