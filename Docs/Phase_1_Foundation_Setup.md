# Phase 1: Foundation Setup (Planning & Execution)

**Goal:** Establish the hardware, software, and basic database structure to support future development.

---

## 1. Set up the Raspberry Pi 5

**Tasks:**

- OS installation
- SSH configuration
- Reserved IP setup (`192.168.1.112`)

**Notes:**

- ✅ SSH is already set up.
- Have you decided on the OS? Options:
  - **Raspberry Pi OS (64-bit)** – Recommended for PostgreSQL stability.
  - **Minimal Debian-based OS** – Might offer better optimization.
  - Other options?

---

## 2. Install PostgreSQL and configure it for external connections

**Tasks:**

- Install PostgreSQL.
- Configure it to allow connections from your development machine.

**Notes:**

- ✅ PostgreSQL was installed but was reinstalled.
- ✅ User `ncacord` is a superuser.
- ✅ Database `cortex_prime` is set up.
- ✅ Remote access is enabled.
- **Security considerations:**
  - **Firewall rules** (limit access to specific IPs)
  - **fail2ban** for brute-force protection
  - **TLS encryption** for remote connections

---

## 3. Database Schema Design (Neural Pathways)

**Tasks:**

- Define tables for nodes, connections, and triggers.
- Decide on JSONB vs. relational schema.
- Explore Graph DB extensions (`pgRouting`, `Apache AGE`).

**Notes:**

- **Core concepts:**
  - **Nodes (Neurons):** Store individual knowledge.
  - **Connections (Synapses):** Define relationships between nodes.
  - **Triggers (Stimuli/Memory Recall):** Enable event-based retrieval.
- **Schema structure:**
  - **Strict Relational (Optimized, but rigid).**
  - **JSONB (Flexible, but harder to index).**
- **Graph DB extensions:**
  - **Apache AGE**: Best for true graph operations (pathfinding, network analysis).
  - **pgRouting**: Best for pathfinding & weighted links.

---

## 4. Research and document PostgreSQL features/extensions

**Tasks:**

- Identify useful extensions (`pgRouting`, `JSONB`, `PostGIS`, etc.).
- Decide on the best storage method.

**Notes:**

- **JSONB** could allow flexible metadata storage.
- Should knowledge be **structured** (predefined schemas) or **unstructured** (dynamic data storage)?

---

## 5. Set up a secure development environment with VS Code

**Tasks:**

- Enable smooth remote SSH integration with VS Code.

**Notes:**

- ✅ Already using VS Code for remote SSH access.
- **Tools in use:**
  - DataGrip ✅
  - pgAdmin (optional?)

---

## 6. Configure automated backups for the database

**Tasks:**

- Set up a backup strategy.

**Notes:**

- ✅ **Backups are stored on an external USB drive (`sda1`).**
- **Automated cron job:**

  ```sh
  0 0 * * * /bin/bash -c 'pg_dump -U ncacord -d cortex_prime -F c -f /media/ncacord/BE7B-0C37/db_backup_$(date +%F).dump'
  ```

- **Future considerations:**
  - Encrypt backups for security.
  - Test restore procedures.

---

## 7. Define and implement naming conventions

**Tasks:**

- Establish clear naming conventions for database objects (tables, triggers, functions).

**Notes:**

- **Standardization:**
  - `snake_case` vs. `camelCase`?
  - Prefixes for different object types (`tbl_`, `trg_`, etc.)?

---

## 8. Document all database relationships (Neural Pathway Structure)

**Tasks:**

- Define relationships between nodes, triggers, and pathways.

**Notes:**

- Should we create an **ERD (Entity Relationship Diagram)** for better visualization?

---

## 9. Write scripts for populating the database with sample data

**Tasks:**

- Create test data for development and validation.

**Notes:**

- Should test data be **random** or **structured**?
- Example data ideas:
  - Neural pathway structures (real-world examples)
  - Associative memory tests

---

## 10. Perform initial functionality and connectivity tests

**Tasks:**

- Verify database functionality before moving to the next phase.

**Notes:**

- Suggested test cases:
  - Insert/query nodes and relationships.
  - Check retrieval speed on a small dataset.
  - Ensure indexing for fast lookups.

---

## **Next Steps: PostgreSQL Reinstallation Plan**

### Steps to Remove and Reinstall PostgreSQL

1. **Uninstall PostgreSQL**  

   ```sh
   sudo systemctl stop postgresql
   sudo apt remove --purge postgresql postgresql-* -y
   sudo rm -rf /var/lib/postgresql/
   sudo rm -rf /etc/postgresql/
   ```

2. **Reinstall PostgreSQL**  

   ```sh
   sudo apt update
   sudo apt install postgresql -y
   ```

3. **Verify Installation**  

   ```sh
   sudo systemctl start postgresql
   sudo systemctl enable postgresql
   sudo systemctl status postgresql
   ```

4. **Reconfigure PostgreSQL (if needed)**  
   - Modify `postgresql.conf` for remote connections.
   - Update `pg_hba.conf` to allow trusted IPs.

---

## **Discussion Points**

1. **What OS will you use on the Raspberry Pi?**
2. **Should we use a graph extension (`pgRouting`, `Apache AGE`)?**
3. **How should data be structured—strict schemas or flexible JSONB storage?**
4. **What’s the best backup strategy?**
5. **Should we generate an ERD for better visualization?**
6. **What kind of test data should be used?**

---

End of Phase 1 Planning Document.
