To handle user validation on the backup website while ensuring continuity and security, the database schema should include tables for users, sessions, and associations that ensure data integrity and relationships. Here’s an updated schema focusing on user validation and seamless switching:

### Database Schema for Backup Website

#### 1. Users
Stores user information, synchronized with the main website.

```sql
CREATE TABLE Users (
    user_id INT PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone VARCHAR(15),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

#### 2. User_Sessions
Tracks user sessions to maintain session continuity.

```sql
CREATE TABLE User_Sessions (
    session_id VARCHAR(255) PRIMARY KEY,
    user_id INT,
    login_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_activity TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);
```

#### 3. User_Accounts
Links user accounts between the primary and backup systems.

```sql
CREATE TABLE User_Accounts (
    account_id INT PRIMARY KEY,
    user_id INT,
    main_user_id INT,  -- Reference to the user ID on the main website
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);
```

#### 4. Orders
Stores order details, synchronized with the main website.

```sql
CREATE TABLE Orders (
    order_id INT PRIMARY KEY,
    user_id INT,
    symbol VARCHAR(10),
    order_type ENUM('buy', 'sell'),
    quantity INT,
    price DECIMAL(10, 2),
    status ENUM('pending', 'executed', 'cancelled') DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);
```

#### 5. Trades
Keeps track of executed trades, synchronized with the main website.

```sql
CREATE TABLE Trades (
    trade_id INT PRIMARY KEY,
    order_id INT,
    user_id INT,
    symbol VARCHAR(10),
    trade_type ENUM('buy', 'sell'),
    quantity INT,
    price DECIMAL(10, 2),
    trade_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (order_id) REFERENCES Orders(order_id),
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);
```

#### 6. Positions
Stores user positions, synchronized with the main website.

```sql
CREATE TABLE Positions (
    position_id INT PRIMARY KEY,
    user_id INT,
    symbol VARCHAR(10),
    quantity INT,
    average_price DECIMAL(10, 2),
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);
```

#### 7. Sync_Log
Tracks synchronization status between primary and backup databases.

```sql
CREATE TABLE Sync_Log (
    sync_id INT PRIMARY KEY,
    table_name VARCHAR(50),
    last_sync TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    status ENUM('success', 'failure') DEFAULT 'success',
    message TEXT
);
```

### Relationships and Associations

- **Users to User_Sessions**: One-to-Many
  - A user can have multiple active sessions.
  - `Users.user_id` -> `User_Sessions.user_id`

- **Users to User_Accounts**: One-to-One
  - Each user on the backup website corresponds to a user on the main website.
  - `Users.user_id` -> `User_Accounts.user_id`
  - `User_Accounts.main_user_id` -> Main website's user ID

- **Users to Orders**: One-to-Many
  - A user can place multiple orders.
  - `Users.user_id` -> `Orders.user_id`

- **Orders to Trades**: One-to-Many
  - An order can result in multiple trades.
  - `Orders.order_id` -> `Trades.order_id`

- **Users to Positions**: One-to-Many
  - A user can have multiple positions.
  - `Users.user_id` -> `Positions.user_id`

### Step-by-Step Workflow Example for User Validation

#### User Login to Backup Website

1. **User** accesses the backup website and enters credentials.
2. **Frontend** collects credentials and sends them to the **Load Balancer**.
3. **Load Balancer** directs the request to a **Web Server**.
4. **Web Server** forwards the request to the **Application Server**.
5. **Application Server** validates credentials against the `Users` table.
6. **Application Server** checks for an active session in the `User_Sessions` table:
   - If an active session exists, update `last_activity` timestamp.
   - If no active session, create a new entry in `User_Sessions`.
7. **Application Server** returns a session token to the **Frontend**.

#### Order Placement and Synchronization

1. **User** places an order via the **Frontend**.
2. Order details are sent through the **Load Balancer** to the **Web Server**.
3. **Web Server** forwards the request to the **Application Server**.
4. **Application Server** saves the order in the `Orders` table.
5. If the order is executed, entries are created in the `Trades` table.
6. **Application Server** updates the `Positions` table accordingly.
7. Changes are queued for synchronization and sent to both the primary and backup databases.
8. **Sync_Log** table is updated with synchronization status.

This detailed design ensures that users can be validated seamlessly when switching to the backup website, with all necessary data relationships and synchronization mechanisms in place to maintain consistency and integrity.








https://www.hackerearth.com/challenges/test/programming-test-golang-test/?login=7003e561d58ae73906a85ed333f01f16


## High-Level Design (HLD) for Order Square-off Backup Website

**1. System Overview**

This document outlines the high-level design for a backup website that enables users of ICICI Securities' retail trading platform to square-off orders during outages of the primary website. The backup system operates on a separate technology stack and activates automatically upon detecting a failure in the main website infrastructure.

**2. Components**

* **User Interface (Frontend):** 
    * Technology: A lightweight framework like React or Vue.js
    * Pros: Enables fast development and responsive design.
    * Cons: Requires additional effort for complex functionalities.
    * Authentication Mechanism: Secure token-based authentication (e.g., JWT) with two-factor authentication (2FA) for enhanced security.
* **Web Servers:** 
    * Technology: Nginx (or similar load balancer)
    * Configuration: Configured for high availability with load balancing across multiple web server instances for scalability.
    * Security Settings: Implement security measures like firewalls, intrusion detection systems (IDS), and regular security updates.
* **Application Servers:** 
    * Technology: Microservices architecture with a framework like Spring Boot or Node.js.
    * Design Pattern: RESTful API design for modularity and easier maintenance.
    * Services:
        * User Management Service: Handles user login, registration, and account details.
        * Order Management Service: Processes order square-off requests.
        * Market Data Service (Optional): Provides real-time market data for user information (if not retrieved directly from the exchange API).
    * Communication: Microservices communicate through asynchronous messaging using a messaging queue like Apache Kafka or RabbitMQ.
* **Database:** 
    * Technology: A high-performance relational database like PostgreSQL
    * Schema Design: Normalized schema with separate tables for users, positions, orders, and audit logs. Data should be a subset of the main database, focusing on information crucial for order square-off (e.g., user ID, position details, order status).
    * Data Synchronization: Incremental replication from the main database using database mirroring or log shipping techniques for near real-time data updates.
* **Messaging Queue:** 
    * Technology: Apache Kafka or RabbitMQ for reliable message delivery between microservices.
    * Queues: Separate queues for different functionalities (e.g., order placement, data updates).
* **Monitoring System:** 
    * Technology: A monitoring tool like Prometheus or Datadog
    * Metrics: Monitor web server health, application server performance, database load, and queue backlog.
    * Alerting Rules: Configure alerts to notify IT teams of potential issues and trigger automatic system failover.

**3. System Flow**

1. User attempts to access the trading platform.
2. Load balancer directs the user request to an available web server instance.
3. Web server forwards the request to the application server.
4. If it's the primary website:
    * Application server interacts with the main database and exchange API for real-time data and order execution.
5. If it's the backup website:
    * User Management Service authenticates the user through secure token-based authentication (JWT) with 2FA.
    * Order Management Service retrieves relevant user position and order data from the backup database.
    * User interacts with the user interface to view positions and place square-off orders.
    * Order Management Service validates the order request and sends it to the messaging queue.
    * A separate process consumes messages from the queue and relays order information to the exchange API (if real-time market data integration is not implemented).
6. Monitoring System continuously monitors the health of the primary website infrastructure.
7. Upon detecting a failure, the Monitoring System triggers automatic failover to the backup website.
    * Load balancer starts directing user traffic to the backup web servers.
8. Once the primary website is restored:
    * Backup database updates are synchronized with the main database using pre-defined mechanisms.

**4. Additional Considerations**

* Security: Implement comprehensive security measures throughout the system, including data encryption at rest and in transit, user access controls, and regular penetration testing.
* Disaster Recovery: Design a disaster recovery plan to address potential outages beyond the scope of this backup website solution (e.g., natural disasters, cyberattacks).
* Performance Testing: Conduct performance testing to ensure the backup website can handle peak user loads during failover scenarios.

**5. Next Steps**

* Develop a detailed Low-Level Design (LLD) for each component, specifying technical configurations and functionalities.
* Implement and test the backup website solution.
* Integrate the system with existing monitoring and alerting infrastructure.
* Conduct disaster recovery drills to ensure team preparedness for switching to the backup website.

**Note:** This HLD provides a high-
## Database Schema for Order Square-off Backup Website

Here's a sample database schema for your Order Square-off Backup Website:

**Tables:**

1. **Users:**
    * Columns:
        * user_id (INT PRIMARY KEY): Unique identifier for each user.
        * username (VARCHAR(255) UNIQUE): Username for login.
        * password_hash (VARCHAR(255)): Hashed password for secure storage.
        * salt (VARCHAR(255)): Salt used for password hashing.
        * email (VARCHAR(255) UNIQUE): User's email address.
        * created_at (DATETIME): Timestamp of user creation.
        * updated_at (DATETIME): Timestamp of user information update.

2. **Positions:**
    * Columns:
        * position_id (INT PRIMARY KEY): Unique identifier for each user position.
        * user_id (INT FOREIGN KEY REFERENCES Users(user_id)): Links position to a specific user.
        * symbol (VARCHAR(20)): Stock symbol for the position.
        * quantity (INT): Number of shares held in the position.
        * average_price (DECIMAL(10,2)): Average purchase price of the position shares.
        * market_price (DECIMAL(10,2)): Current market price of the stock (optional, can be retrieved from exchange API if not stored).
        * created_at (DATETIME): Timestamp of position creation.
        * updated_at (DATETIME): Timestamp of position update (e.g., quantity change).

3. **Orders:**
    * Columns:
        * order_id (INT PRIMARY KEY): Unique identifier for each order.
        * user_id (INT FOREIGN KEY REFERENCES Users(user_id)): Links order to a specific user.
        * position_id (INT FOREIGN KEY REFERENCES Positions(position_id)): Links order to a specific position.
        * order_type (ENUM('BUY', 'SELL')): Type of order (buy or sell).
        * quantity (INT): Number of shares for the order.
        * price (DECIMAL(10,2)): Order price (optional, can be blank for market orders).
        * status (ENUM('PENDING', 'FILLED', 'CANCELED')): Current status of the order.
        * created_at (DATETIME): Timestamp of order creation.
        * updated_at (DATETIME): Timestamp of order status update.

4. **Audit Logs (Optional):**
    * Columns:
        * log_id (INT PRIMARY KEY): Unique identifier for each log entry.
        * user_id (INT FOREIGN KEY REFERENCES Users(user_id)): Links log entry to a specific user (if applicable).
        * action (VARCHAR(255)): Description of the action logged (e.g., login attempt, order placement, square-off).
        * timestamp (DATETIME): Timestamp of the logged action.
        * details (TEXT): Optional additional details about the logged action.

**Relationships:**

* A User can have many Positions.
* A User can have many Orders.
* A Position can have many Orders (related to the same symbol).
* An Order belongs to a single User and a single Position.

**Data Synchronization:**

Since this is a backup system, the data schema focuses on information crucial for order square-off functionalities. You can implement mechanisms like database mirroring or log shipping to achieve near real-time data synchronization between the primary and backup databases for tables like Users, Positions, and Orders.

**Additional Considerations:**

* You can add indexes to frequently queried columns for improved performance.
* Implement proper data type selection based on the expected data range and precision requirements.
* Consider user privacy regulations when storing user information.

**This schema provides a foundation for the backup database. You can further customize it based on your specific requirements and additional functionalities.**




## Component Diagrams for Order Square-off Backup Website

Here's a breakdown of the system components with their interactions represented in diagrams:

**1. User Interface (Frontend):**

* This component is not represented in a separate diagram as it resides within the web server tier.

**2. Web Servers (Web Server Diagram):**

```
+--------------------+      +-----------------------+
| User               |----->| Load Balancer (NGINX) |
+--------------------+      +-----------------------+
                             |
                             | (Distributes traffic)
                             |
                             v
+--------------------+      +-----------------------+
| Web Server Instance 1 |----->| Application Servers   |
+--------------------+      +-----------------------+
                             |
                             v
+--------------------+      +-----------------------+
| Web Server Instance N |----->| Application Servers   |
+--------------------+      +-----------------------+
```

* This diagram shows a load balancer (e.g., NGINX) distributing user traffic across multiple web server instances for scalability and redundancy.

**3. Application Servers (Microservices Diagram):**

```
+--------------------+      +-----------------------+      +-----------------------+      +--------------------+
| User Management     |----->| Messaging Queue        |----->| Order Management      |----->| Database (PostgreSQL)|
| Service (JWT Auth) |       (Kafka/RabbitMQ)       |      Service (Order Logic)  |       (User Positions)    |
+--------------------+      +-----------------------+      +-----------------------+      +--------------------+
                             |                          ^ (Optional, Market Data)
                             |                          |
                             v                          v
+--------------------+      +-----------------------+      +--------------------+
| Market Data Service  |----->| Messaging Queue        |----->| User Interface         |       (Real-time data)  |
+--------------------+       (Kafka/RabbitMQ)       |                         (Frontend)  |
                             |                          +--------------------+
                             v                          

+--------------------+      +--------------------+
| Monitoring System   |----->| Alerting System    |
+--------------------+      +--------------------+

```

* This diagram depicts a microservices architecture with separate services for User Management, Order Management (optional Market Data Service), and User Interface.
* Services communicate asynchronously through a messaging queue (e.g., Kafka/RabbitMQ).
* The Monitoring System tracks application and system health, sending alerts to the Alerting System for potential issues.

**4. Database (Not applicable):**

* The database itself doesn't require a separate component diagram as it interacts with the application servers directly.

**5. Messaging Queue (Not applicable):**

* Similar to the database, the messaging queue operates behind the scenes facilitating communication between services and doesn't require a dedicated diagram.  

**6. Monitoring System (Not applicable):**

* The Monitoring System works in the background and doesn't necessitate a separate component diagram.

**Explanation:**

* The user interacts with the web interface, which resides within the web server tier.
* The web server forwards requests to the application servers.
* User Management handles authentication and authorization.
* Order Management processes order placement requests and interacts with the messaging queue.
* The Market Data Service (optional) retrieves real-time market data from the exchange API (not shown).
* The messaging queue facilitates asynchronous communication between services.
* The database stores user information, positions, and order details.
* The Monitoring System tracks system health and triggers alerts for potential issues.

These component diagrams provide a visual representation of how different parts of the backup website interact to enable order square-off functionalities during primary website outages.


## Design Diagram for Order Square-Off Backup Website

Here's a consolidated design diagram combining the component interactions and workflow for a clear visual representation:

```
+--------------------+      +-----------------------+      +--------------------+      +--------------------+      +--------------------+
| User (Primary)     |----->| Load Balancer (NGINX) |----->| Web Server (Primary)|----->| Application Servers |----->| Database (Primary) |
+--------------------+      +-----------------------+      +--------------------+      +--------------------+      +--------------------+
                             |                          ^ (Real-time data)
                             |                          |
                             v                          v
+--------------------+      +-----------------------+      +--------------------+      +--------------------+      +--------------------+
| User (Backup)      |----->| Load Balancer (NGINX) |----->| Web Server (Backup) |----->| Application Servers |----->| Database (Backup) |
+--------------------+      +-----------------------+      +--------------------+      +--------------------+
                             |                          | (Synchronized Data)
                             |                          v
                             v                          +--------------------+
+--------------------+      +-----------------------+      +--------------------+      +--------------------+
| Monitoring System   |----->| Alerting System        |      | Messaging Queue     |      | Market Data Service |
+--------------------+      +-----------------------+      +--------------------+      +--------------------+
                             |                          ^ (Optional)
                             |                          |
                             v                          v
+--------------------+      +-----------------------+      +--------------------+
| Exchange API        |                             |      | Order Management  |
+--------------------+                             |      | Service             |
                                                     v      +--------------------+
                                                     +---->  | User Interface     | (Frontend)
                                                            +--------------------+

**Legenda:**

* Solid lines represent user requests and system responses.
* Dashed lines represent data flow within the system or synchronization between databases.
* Rectangles represent system components.
* Rounded rectangles represent external entities (e.g., user, exchange API).

**Workflow:**

1. User attempts to access the trading platform (Primary or Backup).
2. Load balancer distributes the request to an available web server.
3. **Primary Website:**
    - Web server interacts with primary application servers and database for real-time data and order execution.
4. **Backup Website:**
    - Web server interacts with backup application servers and database for order processing.
5. User Management Service authenticates the user (Backup Website).
6. User interacts with User Interface to view positions and place square-off orders.
7. Order Management Service retrieves relevant data and interacts with the messaging queue.
8. A separate process consumes messages and relays order information to the exchange API.
9. (Optional) Market Data Service retrieves real-time data from the exchange API (Backup Website).
10. Exchange API acknowledges order execution.
11. Order Management Service updates order status in the backup database.
12. User interface displays confirmation (Backup Website).
13. Monitoring System tracks system health and sends alerts.
14. (Optional) Upon primary website recovery, synchronize backup database with the main database.
```

This design diagram provides a comprehensive overview of the backup website architecture and its interaction with the primary website and exchange API during a failover scenario.
