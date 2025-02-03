# **Simplified Task List for Webpage Improvements**

## **Table of Contents**

1. [Use `asyncpg` – Replace `psycopg2` with `asyncpg` for better performance](#1-use-asyncpg--replace-psycopg2-with-asyncpg-for-better-performance)
2. [Secure Credentials – Store database credentials in environment variables](#2-secure-credentials--store-database-credentials-in-environment-variables)
3. [Optimize WebSocket Queries – Use `LISTEN/NOTIFY` instead of constant polling](#3-optimize-websocket-queries--use-listennotify-instead-of-constant-polling)
4. [Improve UI Styling – Add CSS (e.g., Tailwind) for a cleaner look](#4-improve-ui-styling--add-css-eg-tailwind-for-a-cleaner-look)
5. [Auto-Scroll Updates – Ensure new messages appear at the bottom of the log](#5-auto-scroll-updates--ensure-new-messages-appear-at-the-bottom-of-the-log)
6. [Add User Authentication – Implement a login system for user-specific logs](#6-add-user-authentication--implement-a-login-system-for-user-specific-logs)
7. [Metrics – Track and display database metrics (e.g., query times, connections, total nodes, time till next backup)](#7-metrics--track-and-display-database-metrics-eg-query-times-connections-total-nodes-time-till-next-backup)
8. [Advanced Disk Usage – Display disk usage per node and total disk usage, highlighting high-usage areas](#8-advanced-disk-usage--display-disk-usage-per-node-and-total-disk-usage-highlighting-high-usage-areas)
9. [Search Functionality – Allow users to search logs and database records via the web interface](#9-search-functionality--allow-users-to-search-logs-and-database-records-via-the-web-interface)
10. [Node Visualization – Implement an interactive graph to visually explore database relationships](#10-node-visualization--implement-an-interactive-graph-to-visually-explore-database-relationships)
11. [Query Execution from UI – Enable users to run and test queries directly from the web interface](#11-query-execution-from-ui--enable-users-to-run-and-test-queries-directly-from-the-web-interface)
12. [Error Logging & Display – Show errors and warnings from the database in a structured UI log](#12-error-logging--display--show-errors-and-warnings-from-the-database-in-a-structured-ui-log)
13. [Data Export Feature – Allow users to export query results to CSV, JSON, or other formats](#13-data-export-feature--allow-users-to-export-query-results-to-csv-json-or-other-formats)
14. [Pagination for Logs – Improve log display by implementing pagination for better performance](#14-pagination-for-logs--improve-log-display-by-implementing-pagination-for-better-performance)
15. [Dark Mode Support – Add a toggle for light/dark mode themes in the UI](#15-dark-mode-support--add-a-toggle-for-lightdark-mode-themes-in-the-ui)
16. [User Role Management – Assign roles with different access levels (e.g., admin, viewer)](#16-user-role-management--assign-roles-with-different-access-levels-eg-admin-viewer)
17. [Configurable Notifications – Add alerts for important database events (e.g., high query times)](#17-configurable-notifications--add-alerts-for-important-database-events-eg-high-query-times)
18. [System Health Dashboard – Create a page summarizing system health, storage, and performance metrics](#18-system-health-dashboard--create-a-page-summarizing-system-health-storage-and-performance-metrics)
19. [API for External Access – Develop a REST/GraphQL API for external tools to interact with the system](#19-api-for-external-access--develop-a-restgraphql-api-for-external-tools-to-interact-with-the-system)
20. [Live Backup Monitoring – Show the status and progress of database backups in real-time](#20-live-backup-monitoring--show-the-status-and-progress-of-database-backups-in-real-time)
21. [HTTPS Integration – Secure WebSocket communication with HTTPS](#21-https-integration--secure-websocket-communication-with-https)

---

### **1. Use `asyncpg` – Replace `psycopg2` with `asyncpg` for better performance**  

**What it improves:**  

- `asyncpg` is an asynchronous PostgreSQL driver, while `psycopg2` is synchronous.  
- Switching to `asyncpg` allows FastAPI to handle multiple database queries concurrently without blocking execution.  
- Reduces query response times, especially for WebSocket connections.  
- Improves scalability when handling multiple simultaneous requests.  

**Why it's needed:**  

- `psycopg2` can cause bottlenecks, particularly in a WebSocket-based system where real-time updates are crucial.  
- `asyncpg` provides better performance and is natively supported in async frameworks like FastAPI.  

---

### **2. Secure Credentials – Store database credentials in environment variables**  

**What it improves:**  

- Prevents database credentials from being exposed in the source code.  
- Enhances security by avoiding hardcoded sensitive data.  
- Allows different environments (development, testing, production) to have different credentials without modifying code.  

**Why it's needed:**  

- Storing credentials in code increases the risk of accidental leaks (e.g., pushing to GitHub).  
- Using environment variables ensures a safer, more flexible approach.  

---

### **3. Optimize WebSocket Queries – Use `LISTEN/NOTIFY` instead of constant polling**  

**What it improves:**  

- Eliminates the need to repeatedly query the database, reducing CPU and memory usage.  
- Provides real-time updates directly from PostgreSQL when changes occur.  
- Makes WebSocket communication more efficient and scalable.  

**Why it's needed:**  

- The current setup queries the database in a loop, which is inefficient and resource-heavy.  
- `LISTEN/NOTIFY` allows PostgreSQL to push updates only when relevant changes happen.  

---

### **4. Improve UI Styling – Add CSS (e.g., Tailwind) for a cleaner look**  

**What it improves:**  

- Makes the UI more modern, readable, and visually appealing.  
- Provides responsive design for better mobile and tablet support.  
- Improves layout consistency and user experience.  

**Why it's needed:**  

- The current UI is plain and lacks styling, making it harder to navigate.  
- Tailwind CSS provides a fast way to apply consistent styling with minimal effort.  

---

### **5. Auto-Scroll Updates – Ensure new messages appear at the bottom of the log**  

**What it improves:**  

- Enhances user experience by automatically scrolling to show the latest updates.  
- Ensures that new logs are always visible without requiring manual scrolling.  

**Why it's needed:**  

- Without auto-scrolling, users have to manually scroll down to see new WebSocket updates, which is inconvenient.  

---

### **6. Add User Authentication – Implement a login system for user-specific logs**  

**What it improves:**  

- Allows different users to have personalized logs and access levels.  
- Secures access to sensitive database logs and controls who can view or modify data.  

**Why it's needed:**  

- Right now, the page is accessible to anyone, posing security risks.  
- Authentication enables future features like role-based access control (RBAC).  

---

### **7. Metrics – Track and display database metrics (e.g., query times, connections, total nodes, time till next backup)**  

**What it improves:**  

- Provides insights into database performance and activity.  
- Helps optimize queries and prevent slow performance issues.  

**Why it's needed:**  

- Tracking these metrics will help diagnose issues and improve system efficiency.  
- The backup countdown ensures users are aware of the next scheduled backup.  

---

### **8. Advanced Disk Usage – Display disk usage per node and total disk usage, highlighting high-usage areas**  

**What it improves:**  

- Helps identify where storage is being used the most.  
- Prevents unnecessary data growth in high-usage areas.  
- Allows better disk space management by showing data-heavy tables or logs.  

**Why it's needed:**  

- Without this, storage issues could arise without warning.  
- It ensures efficient use of database space and avoids unnecessary bloating.  

---

### **9. Search Functionality – Allow users to search logs and database records via the web interface**  

**What it improves:**  

- Makes it easier to locate specific logs or data entries quickly.  
- Reduces time spent manually scrolling through records.  

**Why it's needed:**  

- As logs grow, manually finding specific data becomes inefficient.  
- Search functionality improves usability and debugging.  

---

### **10. Node Visualization – Implement an interactive graph to visually explore database relationships**  

**What it improves:**  

- Displays database connections in a visual, intuitive format.  
- Helps users understand complex relationships between nodes.  

**Why it's needed:**  

- The project is modeled after a neural network, so visualizing nodes will improve understanding and usability.  

---

### **11. Query Execution from UI – Enable users to run and test queries directly from the web interface**  

**What it improves:**  

- Allows quick testing and debugging without using external tools.  
- Reduces dependency on a separate SQL client like DataGrip.  

**Why it's needed:**  

- Running queries from the UI makes development and testing more efficient.  

---

### **12. Error Logging & Display – Show errors and warnings from the database in a structured UI log**  

**What it improves:**  

- Provides clear visibility into system errors for troubleshooting.  
- Reduces the time needed to debug and resolve issues.  

**Why it's needed:**  

- Currently, errors may not be visible in real-time, making debugging harder.  

---

### **13. Data Export Feature – Allow users to export query results to CSV, JSON, or other formats**  

**What it improves:**  

- Enables easy data sharing and analysis.  
- Allows exporting logs or query results for further processing.  

**Why it's needed:**  

- Users might need query results outside the web interface for reporting or analysis.  

---

### **14. Pagination for Logs – Improve log display by implementing pagination for better performance**  

**What it improves:**  

- Prevents the UI from slowing down when displaying large amounts of data.  
- Allows for efficient navigation through logs.  

**Why it's needed:**  

- Without pagination, logs could become too large and slow the page down.  

---

### **15. Dark Mode Support – Add a toggle for light/dark mode themes in the UI**  

**What it improves:**  

- Enhances readability and user experience in different lighting conditions.  

**Why it's needed:**  

- Many users prefer dark mode, especially for extended use.  

---

### **16. User Role Management – Assign roles with different access levels (e.g., admin, viewer)**  

**What it improves:**  

- Controls who can perform specific actions within the system.  

**Why it's needed:**  

- Prevents unauthorized access to critical functions.  

---

### **17. Configurable Notifications – Add alerts for important database events (e.g., high query times)**  

**What it improves:**  

- Notifies users of critical system events before they become issues.  

**Why it's needed:**  

- Improves monitoring and responsiveness to potential database slowdowns.  

---

### **18. System Health Dashboard – Create a page summarizing system health, storage, and performance metrics**  

**What it improves:**  

- Provides a one-stop overview of key system statistics.  

**Why it's needed:**  

- Makes monitoring system health more convenient and proactive.  

---

### **19. API for External Access – Develop a REST/GraphQL API for external tools to interact with the system**  

**What it improves:**  

- Enables third-party applications to retrieve and send data programmatically.  

**Why it's needed:**  

- Provides integration options with other tools and services.  

---

### **20. Live Backup Monitoring – Show the status and progress of database backups in real-time**  

**What it improves:**  

- Ensures visibility into backup operations.  

**Why it's needed:**  

- Prevents failed backups from going unnoticed.  

---

### **21. HTTPS Integration – Secure WebSocket communication with HTTPS**  

**What it improves:**  

- Encrypts communication between the client and server.  

**Why it's needed:**  

- Necessary for security once authentication is implemented.  

---
