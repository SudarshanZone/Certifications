+---------------------+       +---------------------+
|    main.go          |       |   websocket_handler |  <---------+
|                     |       |.go                  |            |
|  Initializes DB     |       |                     |            |
|  Sets up Routes     |       | Handles WebSocket   |            |
|                     |       | Connections         |            |
+-----+-----------+---+       +----+------------+---+            |
      |           |                 |            |                |
      v           v                 |            |                |
+-----+-----------+---+             |            |                |
| user_handler.go    |             +|------------v------------+   |
| trade_handler.go   |              |  Stock Price Simulation |   |
| transaction_handler|              |  (Sends price updates   |   |
| .go                |              |  to WebSocket clients)  |   |
+-----+-----------+---+             +-------------------------+   |
      |           |                                                  |
      v           v                                                  |
+-----+-----------+---+                                              |
|  db.go             |                                               |
|  (Database Conn)   |                                               |
+-----+-----------+---+                                              |
      |           |                                                  |
      v           v                                                  |
+-----+-----------+---+                                              |
|  user.go           |                                               |
|  trade.go          |                                               |
|  transaction.go    |                                               |
+---------------------+                                              |
                                                                     |
                                                                     |
PostgreSQL Database <------------------------------------------------+



+---------------------+
| index.js            |
|                     |
|  Entry Point        |
|  Renders App        |
|                     |
+---------+-----------+
          |
          v
+---------+-----------+
| RealTimePriceDisplay|
| .js                 |
|                     |
|  React Component    |
|  Displays Stock     |
|  Prices in Real-time|
+---------+-----------+
          |
          v
+---------+-----------+
| websocketService.js |
|                     |
|  Connects to        |
|  WebSocket Server   |
|  Receives Updates   |
|                     |
+---------------------+


+---------------------+       +---------------------+       +---------------------+
|   Frontend (React)  |       | Backend (Golang)    |       |   Database (Postgre)| 
|                     |       |                     |       |                     |
|  index.js           |<----->| main.go             |<----->| user, trade, trans- |
|                     |       |                     |       | action tables       |
|  RealTimePriceDispl-|       | user_handler.go     |       +---------------------+
|  ay.js              |<----->| trade_handler.go    |
|                     |       | transaction_handler.|
|  websocketService.js|<----->| websocket_handler.go|
+---------------------+       +---------------------+       
      ^                                  ^
      |                                  |
      |                                  |
      +----------------------------------+
           WebSocket Communication
