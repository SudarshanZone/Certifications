Sure, based on the provided calculations and assumptions, here are the filled details:

### Application Server:
- **Count of Servers required:** 30 to 40 servers
- **Application Name:** MyApp
- **Application CAN ID:** (Not provided)
- **Application Tier:** Backend
- **Application ASLC Status:** (Not provided)
- **Zone:** CORP
- **Location:** HYD
- **OS:** Linux
- **OS Version:** Ubuntu 24.04 LTS
- **Environment:** Production
- **Type:** Virtual
- **Server Role:** Application
- **Core (CPU) Per VM:** 8
- **Memory (RAM) Per VM:** 70GB
- **App Storage in GB with Mount Points:** 200GB (assuming)

### Web Server (Assuming load balancing is required):
- **Count of Servers required:** 1
- **Application Name:** MyApp
- **Application CAN ID:** (Not provided)
- **Application Tier:** Frontend
- **Application ASLC Status:** (Not provided)
- **Zone:** CORP
- **Location:** HYD
- **OS:** Linux
- **OS Version:** Ubuntu 24.04 LTS
- **Environment:** Production
- **Type:** Virtual
- **Server Role:** Web
- **Core (CPU) Per VM:** 4 (assuming)
- **Memory (RAM) Per VM:** 16GB (assuming)
- **Web Storage in GB with Mount Points:** 100GB (assuming)
- **Internet Facing:** Yes
- **DNS Name:** myapp.example.com
- **SSL Required:** Yes
- **Load Balancing Require:** Yes

### Database Server:
- **Count of Servers required:** 1
- **Application Name:** MyApp
- **Application CAN ID:** (Not provided)
- **Application Tier:** Data
- **Application ASLC Status:** (Not provided)
- **Zone:** CORP
- **Location:** HYD
- **OS:** Linux
- **OS Version:** Ubuntu 24.04 LTS
- **Environment:** Production
- **Type:** Virtual
- **Server Role:** Database
- **Core (CPU) Per VM:** 16 (assuming)
- **Memory (RAM) Per VM:** 128GB (assuming)
- **DB Storage in GB with Mount Points:** 20TB (assuming)
- **DB Version:** MySQL 8.0 (assuming)
- **DB Name:** MyAppDB
- **Character Set:** UTF-8

Please adjust the details as needed based on specific requirements or configurations.
