# Phase 1: Database Setup Documentation

## **Overview**

**Goal:** Establish a structured database that mimics neural pathways, allowing for future graph-based and real-time visualization capabilities.

## **1️⃣ Database Structure: Hybrid Relational + JSONB Approach**

### **Schema Decisions**

- **Relational Structure:** Ensures fast querying and structured relationships.
- **JSONB Fields:** Allows flexibility for dynamic metadata and evolving knowledge storage.

### **Final Schema**

```sql
CREATE TABLE nodes (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    data JSONB,  -- Stores neuron metadata
    created_at TIMESTAMP DEFAULT now()
);

CREATE TABLE connections (
    id SERIAL PRIMARY KEY,
    source_id INT REFERENCES nodes(id) ON DELETE CASCADE,
    target_id INT REFERENCES nodes(id) ON DELETE CASCADE,
    weight FLOAT DEFAULT 1.0,  -- Represents connection strength
    metadata JSONB,  -- Stores relationship type and additional attributes
    created_at TIMESTAMP DEFAULT now()
);
```

✅ **Why This Approach?**

- **Mimics neurons & synapses** with nodes & weighted connections.
- **Ensures structured querying** while allowing flexibility with JSONB.

## **2️⃣ Graph Database Extension: Apache AGE**

### **Reason for Choosing Apache AGE**

- Allows **graph-based querying** within PostgreSQL.
- Supports **pathfinding and associative knowledge retrieval.**

### **Installation & Setup**

```sh
sudo apt install postgresql-15-age
```

### **Basic Graph Query Example**

```sql
SELECT * FROM cypher('graph', $$ MATCH (n)-[r]->(m) RETURN n, r, m $$);
```

✅ **Why?**

- Enables **real graph traversal** for neural-like connections.
- Works **directly within PostgreSQL** (no separate database needed).

## **3️⃣ Query Logging for Real-Time Tracking**

### **Why Log Queries?**

- Allows us to track **live database activity.**
- Supports future **real-time visualization of queries.**

### **Enable `pg_stat_statements`**

```sql
CREATE EXTENSION pg_stat_statements;
```

### **Create a Query Log Table**

```sql
CREATE TABLE query_log (
    id SERIAL PRIMARY KEY,
    query TEXT,
    timestamp TIMESTAMP DEFAULT now()
);
```

### **Trigger to Log Queries**

```sql
CREATE OR REPLACE FUNCTION log_queries()
RETURNS event_trigger AS $$
BEGIN
    INSERT INTO query_log (query)
    VALUES (current_query());
END;
$$ LANGUAGE plpgsql;
```

✅ **Why?**

- Provides a **record of every query executed.**
- Can be connected to **real-time visualization tools.**

## **Next Steps**

🔹 **Implement schema in PostgreSQL** and test table creation.  
🔹 **Set up query logging and monitor activity.**  
🔹 **Begin basic graph querying with Apache AGE.**  

---
This document serves as the **official Phase 1 database setup reference** and will be updated as we progress. 🚀
