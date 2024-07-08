[08/07, 10:49] Sudarshan Zarkar: # Detailed Microservices Architecture for IRRA Backup Website

## Overview

The IRRA backup website is designed to ensure business continuity by allowing the operations (ops) team to manage user requests for square-offs during emergencies. The architecture comprises multiple microservices, each handling a specific domain of functionality.

## Microservices Overview

1. **User Service**
2. **Positions Service**
3. **Square-off Service**
4. **Authentication Service**
5. **Data Sync Service**
6. **Notification Service**

## Microservices Architecture

### 1. User Service

**Responsibilities**:
- Manage user data (e.g., personal information, authentication).
- Provide endpoints for fetching user details based on broker ID.
- Authenticate ops team members.

**Endpoints**:
- `GET /users/{brokerId}`: Fetch user details by broker ID.
- `POST /login`: Authenticate ops team member.
- `GET /users/{userId}`: Fetch user details by user ID.

**Data Model**:
```sql
CREATE TABLE Users (
    userId SERIAL PRIMARY KEY,
    brokerId VARCHAR(50) UNIQUE NOT NULL,
    panCard VARCHAR(10) NOT NULL,
    name VARCHAR(100),
    dateOfBirth DATE,
    email VARCHAR(100),
    phone VARCHAR(15),
    hashedPassword VARCHAR(255)
);
```

### 2. Positions Service

**Responsibilities**:
- Manage user positions.
- Provide endpoints for querying open positions.
- Handle updates to positions data.

**Endpoints**:
- `GET /positions/{userId}`: Fetch open positions for a user.
- `POST /positions/update`: Update positions based on square-off actions.

**Data Model**:
```sql
CREATE TABLE Positions (
    positionId SERIAL PRIMARY KEY,
    userId INT REFERENCES Users(userId),
    symbol VARCHAR(50),
    quantity INT,
    openDate DATE,
    status VARCHAR(20) DEFAULT 'open'
);
```

### 3. Square-off Service

**Responsibilities**:
- Handle square-off requests.
- Update the orders table.
- Interact with the exchange via Trade Allocation Processor (TAP).

**Endpoints**:
- `POST /squareoff`: Process square-off requests.

**Data Model**:
```sql
CREATE TABLE Orders (
    orderId SERIAL PRIMARY KEY,
    userId INT REFERENCES Users(userId),
    symbol VARCHAR(50),
    quantity INT,
    orderType VARCHAR(20),
    status VARCHAR(20) DEFAULT 'pending',
    createdAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 4. Authentication Service

**Responsibilities**:
- Handle ops team authentication.
- Provide JWT tokens for authenticated sessions.

**Endpoints**:
- `POST /auth/login`: Authenticate ops team member and provide JWT token.

**Data Model**:
```sql
CREATE TABLE OpsTeam (
    opsId SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    hashedPassword VARCHAR(255),
    role VARCHAR(50)
);
```

### 5. Data Sync Service

**Responsibilities**:
- Ensure real-time data synchronization between the primary and backup databases.
- Handle data dumps and updates.

**Endpoints**:
- `POST /sync/dump`: Perform a database dump before market hours.
- `POST /sync/update`: Update backup database with real-time data from the primary system.

### 6. Notification Service

**Responsibilities**:
- Notify ops team and users about the status of their requests.
- Send alerts and updates.

**Endpoints**:
- `POST /notify/ops`: Notify ops team about system status or user requests.
- `POST /notify/user`: Notify user about the status of their request.

## Detailed Flow

### User Verification and Request Handling

1. **User Contacts Ops Team**:
   - Users with open positions call the ops team using the provided numbers.
   
2. **Ops Team Verification**:
   - Ops team verifies user details (PAN Card, USERID, Date of Birth) via a secure call.

3. **Ops Team Logs into IRRA Website**:
   - Ops team member logs into the IRRA website using their credentials via the Authentication Service.

4. **Ops Team Fetches User Details**:
   - Using the broker ID provided by the user, the ops team fetches user details via the User Service.

5. **Ops Team Handles User Request**:
   - Based on user instructions, the ops team either squares off open positions or informs the user of their open positions.

6. **Square-off Execution**:
   - Ops team submits a square-off request via the IRRA frontend, which interacts with the Square-off Service.
   - The Square-off Service updates the orders table and initiates the process of interacting with the exchange.

7. **Order Processing**:
   - A microservice continuously reads the order table in FIFO manner and changes the flag status of processed orders.
   - The Trade Allocation Processor (TAP) sends the request to the exchange.

8. **Response Handling**:
   - Successful responses from the exchange are stored in a Temp Trade Table.
   - Drop copies modify the Trade Table and Position Table.

9. **Database Synchronization**:
   - The actual response is applied to the Trade Table, and the position table is updated accordingly.

## Sequence Diagram

```
User -> Ops Team: Call for position square-off
Ops Team -> Authentication Service: Login
Authentication Service -> Ops Team: JWT Token
Ops Team -> User Service: Fetch User Details
User Service -> Ops Team: User Details
Ops Team -> IRRA Frontend: Submit Square-off Request
IRRA Frontend -> Square-off Service: Process Request
Square-off Service -> Orders Table: Update Order Status
Orders Table -> Order Processor Microservice: Read Order
Order Processor Microservice -> TAP: Send Request to Exchange
Exchange -> TAP: Send Response and Drop Copy
TAP -> Temp Trade Table: Store Actual Response
TAP -> Trade Table: Update Trade Table
TAP -> Position Table: Update Position Table
TAP -> Orders Table: Update Order Status
Position Table -> IRRA Frontend: Update Position Status
IRRA Frontend -> Ops Team: Confirmation
Ops Team -> User: Confirm Square-off
```

## Conclusion

This detailed microservices architecture ensures a robust, reliable, and scalable backup system for managing order square-offs during emergencies. By leveraging independent microservices, the system can handle various aspects of user requests, data synchronization, and real-time updates efficiently, ensuring business continuity and user satisfaction.
[08/07, 10:54] Sudarshan Zarkar: # Detailed Microservices Architecture for IRRA Backup Website

## Overview

The IRRA backup website is designed to ensure business continuity by allowing the operations (ops) team to manage user requests for square-offs during emergencies. The architecture comprises multiple microservices, each handling a specific domain of functionality. The focus will be on the following microservices:

1. **User Service**
2. **Positions Service**
3. **Square-off Service**
4. **Authentication Service**

## Microservices Architecture

### 1. User Service

**Responsibilities**:
- Manage user data (e.g., personal information, authentication).
- Provide endpoints for fetching user details based on broker ID.
- Authenticate ops team members.

**Endpoints**:
- `GET /users/{brokerId}`: Fetch user details by broker ID.
- `GET /users/{userId}`: Fetch user details by user ID.

**Data Model**:
```sql
CREATE TABLE Users (
    userId SERIAL PRIMARY KEY,
    brokerId VARCHAR(50) UNIQUE NOT NULL,
    panCard VARCHAR(10) NOT NULL,
    name VARCHAR(100),
    dateOfBirth DATE,
    email VARCHAR(100),
    phone VARCHAR(15)
);
```

### 2. Positions Service

**Responsibilities**:
- Manage user positions.
- Provide endpoints for querying open positions.
- Handle updates to positions data.

**Endpoints**:
- `GET /positions/{userId}`: Fetch open positions for a user.
- `POST /positions/update`: Update positions based on square-off actions.

**Data Model**:
```sql
CREATE TABLE Positions (
    positionId SERIAL PRIMARY KEY,
    userId INT REFERENCES Users(userId),
    symbol VARCHAR(50),
    quantity INT,
    openDate DATE,
    status VARCHAR(20) DEFAULT 'open'
);
```

### 3. Square-off Service

**Responsibilities**:
- Handle square-off requests.
- Update the orders table.
- Interact with the exchange via Trade Allocation Processor (TAP).

**Endpoints**:
- `POST /squareoff`: Process square-off requests.

**Data Model**:
```sql
CREATE TABLE Orders (
    orderId SERIAL PRIMARY KEY,
    userId INT REFERENCES Users(userId),
    symbol VARCHAR(50),
    quantity INT,
    orderType VARCHAR(20),
    status VARCHAR(20) DEFAULT 'pending',
    createdAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 4. Authentication Service

**Responsibilities**:
- Handle ops team authentication.
- Provide JWT tokens for authenticated sessions.

**Endpoints**:
- `POST /auth/login`: Authenticate ops team member and provide JWT token.

**Data Model**:
```sql
CREATE TABLE OpsTeam (
    opsId SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    hashedPassword VARCHAR(255),
    role VARCHAR(50)
);
```

## Detailed Flow

### User Verification and Request Handling

1. **User Contacts Ops Team**:
   - Users with open positions call the ops team using the provided numbers.
   
2. **Ops Team Verification**:
   - Ops team verifies user details (PAN Card, USERID, Date of Birth) via a secure call.

3. **Ops Team Logs into IRRA Website**:
   - Ops team member logs into the IRRA website using their credentials via the Authentication Service.

4. **Ops Team Fetches User Details**:
   - Using the broker ID provided by the user, the ops team fetches user details via the User Service.

5. **Ops Team Handles User Request**:
   - Based on user instructions, the ops team either squares off open positions or informs the user of their open positions.

6. **Square-off Execution**:
   - Ops team submits a square-off request via the IRRA frontend, which interacts with the Square-off Service.
   - The Square-off Service updates the orders table and initiates the process of interacting with the exchange.

7. **Order Processing**:
   - A microservice continuously reads the order table in FIFO manner and changes the flag status of processed orders.
   - The Trade Allocation Processor (TAP) sends the request to the exchange.

8. **Response Handling**:
   - Successful responses from the exchange are stored in a Temp Trade Table.
   - Drop copies modify the Trade Table and Position Table.

9. **Database Synchronization**:
   - The actual response is applied to the Trade Table, and the position table is updated accordingly.

## Sequence Diagram

```
User -> Ops Team: Call for position square-off
Ops Team -> Authentication Service: Login
Authentication Service -> Ops Team: JWT Token
Ops Team -> User Service: Fetch User Details
User Service -> Ops Team: User Details
Ops Team -> IRRA Frontend: Submit Square-off Request
IRRA Frontend -> Square-off Service: Process Request
Square-off Service -> Orders Table: Update Order Status
Orders Table -> Order Processor Microservice: Read Order
Order Processor Microservice -> TAP: Send Request to Exchange
Exchange -> TAP: Send Response and Drop Copy
TAP -> Temp Trade Table: Store Actual Response
TAP -> Trade Table: Update Trade Table
TAP -> Position Table: Update Position Table
TAP -> Orders Table: Update Order Status
Position Table -> IRRA Frontend: Update Position Status
IRRA Frontend -> Ops Team: Confirmation
Ops Team -> User: Confirm Square-off
```

## Microservice Implementation Details

### User Service

**Implementation Overview**:
- Use a RESTful API to handle requests.
- Secure endpoints using JWT tokens.
- Use a relational database (e.g., PostgreSQL) for user data storage.

**Example Code**:
```go
// main.go
package main

import (
    "github.com/gin-gonic/gin"
    "net/http"
)

func main() {
    router := gin.Default()

    router.GET("/users/:brokerId", getUserByBrokerId)
    router.GET("/users/:userId", getUserById)

    router.Run(":8080")
}

func getUserByBrokerId(c *gin.Context) {
    brokerId := c.Param("brokerId")
    // Fetch user details from database
    user := getUserDetailsByBrokerId(brokerId)
    c.JSON(http.StatusOK, user)
}

func getUserById(c *gin.Context) {
    userId := c.Param("userId")
    // Fetch user details from database
    user := getUserDetailsById(userId)
    c.JSON(http.StatusOK, user)
}
```

### Positions Service

**Implementation Overview**:
- Use a RESTful API to handle requests.
- Secure endpoints using JWT tokens.
- Use a relational database (e.g., PostgreSQL) for position data storage.

**Example Code**:
```go
// main.go
package main

import (
    "github.com/gin-gonic/gin"
    "net/http"
)

func main() {
    router := gin.Default()

    router.GET("/positions/:userId", getPositionsByUserId)
    router.POST("/positions/update", updatePosition)

    router.Run(":8081")
}

func getPositionsByUserId(c *gin.Context) {
    userId := c.Param("userId")
    // Fetch positions from database
    positions := getPositions(userId)
    c.JSON(http.StatusOK, positions)
}

func updatePosition(c *gin.Context) {
    var position Position
    if err := c.ShouldBindJSON(&position); err == nil {
        // Update position in database
        updatePositionInDB(position)
        c.JSON(http.StatusOK, gin.H{"status": "updated"})
    } else {
        c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
    }
}
```

### Square-off Service

**Implementation Overview**:
- Use a RESTful API to handle requests.
- Secure endpoints using JWT tokens.
- Use a relational database (e.g., PostgreSQL) for order data storage.
- Interact with the exchange using a trade allocation processor (TAP).

**Example Code**:
```go
// main.go
package main

import (
    "github.com/gin-gonic/gin"
    "net/http"
)

func main() {
    router := gin.Default()

    router.POST("/squareoff", processSquareOff)

    router.Run(":8082")
}

func processSquareOff(c *gin.Context) {
    var order Order
    if err := c.ShouldBindJSON(&order); err == nil {
        // Update orders table
        updateOrderInDB(order)
        // Send request to exchange via TAP
        sendToExchange(order)
        c.JSON(http.StatusOK, gin.H{"status": "square-off processed"})
    } else {
        c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
    }
}
```

### Authentication Service

**Implementation Overview**:
- Use a RESTful API to handle requests.
- Provide JWT tokens for authenticated sessions.
- Use a relational database (e.g., PostgreSQL) for ops team data storage.

**Example Code**:
```go
// main.go
package main

import (
    "github.com/gin-gonic/gin"
    "net/http"
)

func main() {
    router := gin.Default()

    router.POST("/auth/login", login)

    router.Run(":8083")
}

func login(c *gin.Context) {
    var credentials Credentials
    if err := c.ShouldBindJSON(&credentials); err == nil {
        // Authenticate user
        if isValidUser(credentials) {
            // Generate JWT token
            token := generateJWTToken(credentials.Username)
            c.JSON(http.StatusOK, gin.H{"token": token})
        } else {
            c.JSON(http.StatusUnauthorized, gin.H{"error": "invalid credentials"})
        }
    } else {
        c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
    }
}
```

## Conclusion

This detailed microservices architecture ensures a robust, reliable, and scalable backup system for managing
