# ROADMAP.md

**Project Codename**: **Cortex**  
**Goal**: A visually interactive, AI-driven knowledge base that can be displayed in a real-time 3D/graph view and run on a Raspberry Pi 5.  

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Phase 1: Brainstorming & Vision](#phase-1-brainstorming--vision)
3. [Phase 2: Environment & Infrastructure Setup](#phase-2-environment--infrastructure-setup)
4. [Phase 3: Data Modeling & Database Setup](#phase-3-data-modeling--database-setup)
5. [Phase 4: Basic CRUD & Querying](#phase-4-basic-crud--querying)
6. [Phase 5: Backend Service / API Layer](#phase-5-backend-service--api-layer)
7. [Phase 6: Front-End Visualization](#phase-6-front-end-visualization)
8. [Phase 7: AI / Assistant Integration](#phase-7-ai--assistant-integration)
9. [Phase 8: Real-Time Graph Animations & UX Polish](#phase-8-real-time-graph-animations--ux-polish)
10. [Phase 9: Security, Deployment & Optimization](#phase-9-security-deployment--optimization)
11. [Phase 10: Maintenance, Monitoring & Future Expansions](#phase-10-maintenance-monitoring--future-expansions)
12. [Appendix: Reference Docs & Further Reading](#appendix-reference-docs--further-reading)

---

## Project Overview

**High-Level Concept**  

- Build a “knowledge cortex” on a Raspberry Pi 5 (8GB) that stores various forms of information (text, relationships, metadata, embeddings).  
- Visualize the data in a 3D graph (similar to Obsidian or a force-directed node graph), with real-time animations when queries or AI traversals occur.  
- Eventually integrate AI capabilities (query suggestions, semantic search, or other LLM-based functionalities).

**Key Components**  

1. **Database**: Could be Neo4j (graph DB), PostgreSQL (relational + pgvector), or a hybrid approach.  
2. **Backend / API**: Handles queries, orchestrates data flow, and manages real-time communications (WebSockets or SSE).  
3. **Front-End**: 3D graph visualization using libraries like [3d-force-graph](https://github.com/vasturiano/3d-force-graph), [three.js](https://threejs.org/), or [Cytoscape.js](https://js.cytoscape.org/) with 3D extensions.  
4. **AI Integration**: Options for vector search, LLM-based question answering, or chat-driven interactions with the knowledge graph.  

---

## Phase 1: Brainstorming & Vision

**UPDATE! PHASE 1 IS COMPLETE! PLEASE SEE THE [PHASE 1 COMPLETION REPORT](Docs/PHASE_1_COMPLETE.md) FOR DETAILS.**

1. **Define the Project Scope**  
   - **What** exact types of data go into the knowledge base (text, PDFs, code snippets, website references, etc.)?  
   - **How** large do you expect it to get? (Tens of thousands of nodes? Millions?)  
   - **Who** will use it? Just you, or others as well?

2. **Identify Core Features**  
   - Real-time, 3D visualization of nodes and relationships.  
   - Graph-based queries (highlighting which relationships are traversed).  
   - AI assistant that can query or “walk” through the data automatically.  
   - Future expansions: collaborative editing, user authentication, multiple knowledge sources, etc.

3. **Decide on the Initial Data Model**  
   - Are you using a purely graph-based approach (e.g., Neo4j)?  
   - Or a relational DB (PostgreSQL) + a graph-like schema?  
   - Possibly a hybrid approach (e.g., Postgres + Qdrant for vectors, or Postgres with pgvector).

4. **Evaluate Resource Constraints**  
   - Raspberry Pi 5 has 8GB RAM; that’s decent, but certain DBs (like large Neo4j or big vector indexes) might demand optimization.  
   - Decide if you’re okay with Docker or prefer native installs.

**Output of Phase 1**:  

- A **conceptual diagram** of your system (data flow from DB -> backend -> front-end -> user).  
- A **technical specification** doc that briefly explains your final DB choice, initial scope, and the “why” behind it.

---

## Phase 2: Environment & Infrastructure Setup

1. **Raspberry Pi OS & System Configuration**  
   - Install the latest 64-bit Raspberry Pi OS.  
   - Update & upgrade packages (`sudo apt update && sudo apt full-upgrade`).  
   - Enable SSH & VNC for remote access.  
   - (Optional) Set up static IP or DHCP reservation so you always know where to reach it.

2. **Database Installation**  
   - **Option A: Neo4j**  

     ```bash
     wget -O - https://debian.neo4j.com/neotechnology.gpg.key | sudo apt-key add -
     echo "deb https://debian.neo4j.com stable 4.x" | sudo tee /etc/apt/sources.list.d/neo4j.list
     sudo apt update
     sudo apt install neo4j
     sudo systemctl enable neo4j
     sudo systemctl start neo4j
     ```

   - **Option B: PostgreSQL (and pgvector)**  

     ```bash
     sudo apt update
     sudo apt install postgresql postgresql-contrib
     ```

     Then install or build pgvector if needed.  
   - (Optional) **Docker** approach if you prefer containerized services.

3. **VS Code Remote Setup**  
   - Install the **Remote - SSH** extension or similar, so you can develop on your Pi from your main machine.  
   - Confirm you can connect to the Pi, open a folder, and run code.

4. **Basic Networking & Security**  
   - If you’re accessing from outside your LAN, consider enabling a firewall (e.g., `ufw`) or set up a VPN.  
   - Change default passwords, use SSH keys, etc.

**Output of Phase 2**:  

- A **running environment** on the Pi where you can connect, run commands, and store data in your chosen database.  
- Document any specific installation/config quirks in `docs/environment-setup.md`.

---

## Phase 3: Data Modeling & Database Setup

1. **Schema / Data Model**  
   - If **Neo4j**: define the node labels, relationship types, and properties. For example:  
     - `(:Article {title: "...", content: "..."})`,  
     - `(:Topic {name: "..."})`,  
     - relationships like `(:Article)-[:TAGGED_WITH]->(:Topic)`.  
   - If **PostgreSQL**: design your tables. For example:  

     ```sql
     CREATE TABLE articles (
       id SERIAL PRIMARY KEY,
       title TEXT,
       content TEXT
       -- maybe embedding VECTOR(...) if using pgvector
     );

     CREATE TABLE topics (
       id SERIAL PRIMARY KEY,
       name TEXT
     );

     CREATE TABLE article_topics (
       article_id INTEGER REFERENCES articles(id),
       topic_id INTEGER REFERENCES topics(id),
       PRIMARY KEY(article_id, topic_id)
     );
     ```

   - Consider future expansions: do you need user accounts, versions, or any specialized indexing?

2. **Data Ingestion / ETL (Optional)**  
   - If you already have documents, notes, or references, plan how you’ll import them into your DB.  
   - Possibly write a small script to parse files and create nodes/rows.

3. **Test the Schema**  
   - Insert sample data.  
   - Run a few basic queries (Cypher or SQL) to ensure everything works.

**Output of Phase 3**:  

- A **database schema** (in `.cypher` or `.sql` files) under `docs/data-model/`.  
- A **few test queries** to confirm data can be inserted, read, and updated.

---

## Phase 4: Basic CRUD & Querying

1. **Simple Command-Line or Scripted Queries**  
   - If Neo4j, use the Cypher shell to confirm you can `CREATE`, `MATCH`, `RETURN`, etc.  
   - If Postgres, use `psql` or a script to run `SELECT`, `INSERT`, `UPDATE`.

2. **Write a Minimal Script / Program**  
   - For example, a Python script or Node.js script that:
     - Connects to the DB,  
     - Inserts some data,  
     - Reads it back,  
     - Logs it to the console.

3. **Establish Basic Query Patterns**  
   - For the knowledge base, you might want “get all articles related to a topic,” “find neighbors of a node,” etc.  
   - Test these to confirm your schema logic is correct.

**Output of Phase 4**:  

- A small program in `scripts/test_db_connection.(py|js)` that verifies your setup.  
- Confidence that CRUD operations function as intended.

---

## Phase 5: Backend Service / API Layer

1. **Decide on the Tech Stack**  
   - Common choices: Node.js (Express, NestJS), Python (Flask, FastAPI), Go, etc.  
   - Must support easy integration with your DB driver (Neo4j driver, `pg` library, etc.).  
   - Also consider real-time capabilities (e.g., WebSockets).

2. **Set Up Basic Server**  
   - Start with a minimal REST API or GraphQL endpoint that can:  
     - **GET** certain items,  
     - **POST** new items,  
     - Possibly a `/search` or `/query` endpoint for more advanced queries.

3. **WebSockets or SSE**  
   - For real-time updates, set up a WebSocket server:  
     - On new data or queries, push an event to the front-end.  
   - Alternatively, implement **Server-Sent Events** (SSE) for a simpler one-way push from server to client.

4. **API Security & Auth (Optional)**  
   - If you plan to open this up to multiple users or the internet, think about JWTs, session tokens, or at least basic auth.  
   - You can skip for a single-user local project but keep it in mind for future expansions.

**Output of Phase 5**:  

- A **functional API** that can be reached from your local machine or any other device.  
- A **WebSocket or SSE** endpoint for real-time data events.  
- Documentation in `docs/backend-api.md` for each endpoint, request/response format, etc.

---

## Phase 6: Front-End Visualization

1. **Select a Visualization Library**  
   - **[3d-force-graph](https://github.com/vasturiano/3d-force-graph)** (JavaScript-based, built on [three.js](https://threejs.org/)).  
   - If you prefer 2D, use something like [D3.js force layout](https://github.com/d3/d3-force) or [Cytoscape.js](https://js.cytoscape.org/).  
   - For a more advanced 3D approach, you could build your own with raw three.js, but that requires heavier custom work.

2. **Frontend Framework or Vanilla JS?**  
   - Decide if you want React, Vue, Angular, or just a simple HTML + JS page.  
   - A minimal approach: a single `index.html` with a `<script>` that imports the 3D graph library and connects to your backend’s WebSocket.

3. **Rendering the Graph**  
   - Structure:  
     - The front-end listens on the WebSocket.  
     - When a message arrives with new/updated node-edge data, it updates the graph.  
   - Decide on the data format. Typically something like:

     ```json
     {
       "nodes": [
         { "id": "node1", "label": "Article", "title": "Sample Title" },
         { "id": "node2", "label": "Topic", "name": "TestTopic" }
       ],
       "links": [
         { "source": "node1", "target": "node2", "label": "TAGGED_WITH" }
       ]
     }
     ```

4. **UI Controls & Basic Interactions**  
   - Zoom, pan, rotate the 3D scene.  
   - Click on a node to see details (title, content, relationships).  
   - Possibly highlight newly queried paths or edges in a bright color for a second or two.

**Output of Phase 6**:  

- A **front-end application** that displays at least a small subgraph from your DB.  
- A **real-time update** mechanism (the graph changes whenever you insert new data or run a “traversal query”).

---

## Phase 7: AI / Assistant Integration

1. **Define AI Use Cases**  
   - **Semantic Search**: embed text and use vector similarity to find relevant nodes/documents.  
   - **LLM Chat**: question answering by retrieving knowledge base context, then feeding it into an LLM.  
   - **Graph Traversal**: an “assistant” that can automatically navigate relationships to find answers.

2. **Vector Search Approach** (if applicable)  
   - If using **Neo4j**: consider [Neo4j Graph Data Science library](https://neo4j.com/developer/graph-data-science/) or external tools for embeddings.  
   - If using **Postgres**: install [pgvector](https://github.com/pgvector/pgvector) to store embeddings in a `VECTOR` column and do similarity searches.  
   - Or a **standalone vector DB** (like Qdrant, Weaviate) to store embeddings, combined with your main DB for metadata.

3. **LLM / Chat Integration**  
   - Possibly use OpenAI’s API or a local LLM (like GPT4All, Llama variants) depending on hardware constraints.  
   - The process:  
     - Parse user query -> embed or interpret -> query knowledge DB -> retrieve relevant text -> feed into LLM for an answer.  
   - For real-time graph “visualization” of the LLM’s path, highlight how it hopped through knowledge nodes.

4. **Backend Architecture for AI**  
   - Typically: user query -> backend function -> retrieve relevant nodes from DB -> build context -> send to LLM -> return LLM response to front-end -> optionally animate the graph path.

5. **Performance Considerations**  
   - Running a large language model locally on a Pi 5 is possible for smaller models but might be slow.  
   - You might offload AI calls to a cloud service or a more powerful machine.

**Output of Phase 7**:  

- A plan (or initial implementation) for how AI will integrate with the knowledge base.  
- Possibly a prototype of “ask a question -> see relevant nodes get highlighted -> read AI answer.”

---

## Phase 8: Real-Time Graph Animations & UX Polish

1. **Highlighting Query Paths**  
   - When the user or AI runs a query, highlight each edge or node in sequence to show the “traversal.”  
   - Animate edges (e.g., a pulsating glow) or briefly color them differently.

2. **Dynamic Subgraph Loading**  
   - For performance, only load relevant parts of the graph at a time (e.g., a node’s immediate neighbors).  
   - Let the user expand nodes manually if they want to explore deeper.

3. **Refine the UI**  
   - Add side panels or pop-ups for node details.  
   - Improve layout, color-coding, and text legibility in 3D space.

4. **Error Handling & Edge Cases**  
   - Handling very large numbers of nodes can bog down the visualization.  
   - Provide fallback or partial load strategies.

**Output of Phase 8**:  

- A polished front-end that cleanly shows query traversals and is user-friendly for exploration.  
- Document the **UX enhancements** and any performance tips in `docs/ui-ux-improvements.md`.

---

## Phase 9: Security, Deployment & Optimization

1. **Security & Authentication**  
   - If the system goes beyond your LAN, integrate user login (JWT tokens or OAuth).  
   - Enable HTTPS if you expose your Pi publicly.

2. **Production Deployment**  
   - Keep your Pi in a stable environment (server cabinet with good cooling).  
   - Possibly run the DB as a system service (or Docker container) at startup.  
   - Configure backups for your DB.

3. **Performance Tuning**  
   - Tweak Neo4j config or Postgres config to better use 8GB RAM (cache sizes, etc.).  
   - Index frequently queried properties or relationships.

4. **Logging & Monitoring**  
   - Monitor logs (backend logs, DB logs) for errors or slow queries.  
   - Optionally set up a lightweight monitoring system like [Prometheus + Grafana] if you want dashboards.

**Output of Phase 9**:  

- A stable, secure deployment of your system that automatically starts on Raspberry Pi boot.  
- A short `docs/deployment-and-security.md` detailing how to set up or replicate in another environment.

---

## Phase 10: Maintenance, Monitoring & Future Expansions

1. **Routine Maintenance**  
   - DB backups, OS updates, library updates.  
   - Keep an eye on disk usage, especially if you’re ingesting a lot of data.

2. **Version Control & CI/CD**  
   - Track your code in Git.  
   - Possibly set up a CI pipeline to run tests or lint code before deploying to the Pi.

3. **Feature Roadmap**  
   - Advanced analytics (graph algorithms, centrality measures, etc.).  
   - Collaborative features (multiple users building the knowledge base).  
   - Additional data sources (APIs, web scraping, file imports, etc.).

4. **Community or Open Source** (Optional)  
   - If you plan on sharing your “Cortex” with others, create documentation, examples, or a public repo.  
   - Possibly accept contributions from the community.

**Output of Phase 10**:  

- A well-maintained codebase with a plan for further growth.  
- Additional docs as needed for new features or collaborations.

---

## Appendix: Reference Docs & Further Reading

1. **Neo4j**  
   - [Neo4j Documentation](https://neo4j.com/docs/)  
   - [Neo4j Graph Data Science](https://neo4j.com/developer/graph-data-science/)

2. **PostgreSQL**  
   - [Official PostgreSQL Docs](https://www.postgresql.org/docs/current/)  
   - [pgvector GitHub](https://github.com/pgvector/pgvector)

3. **Visualization Libraries**  
   - [3d-force-graph GitHub](https://github.com/vasturiano/3d-force-graph)  
   - [three.js Docs](https://threejs.org/docs/)  
   - [D3.js Force Layout](https://github.com/d3/d3-force)  
   - [Cytoscape.js](https://js.cytoscape.org/)

4. **AI Integration**  
   - [OpenAI API Docs](https://platform.openai.com/docs/introduction)  
   - [Local LLMs (GPT4All, LLaMA, etc.) Tutorials](https://github.com/nomic-ai/gpt4all)

5. **Example Projects**  
   - [Knowledge Graph + React + Neo4j Starter](https://github.com/neo4j-graphacademy)  
   - [3D Force Graph Examples](https://blocks.roadtolarissa.com/vasturiano)

---

### Next Steps

- **Use this roadmap as a living document**. As you work through each phase, create dedicated `.md` files in your `docs/` folder for deeper details (e.g., `docs/ai-integration.md`, `docs/visualization-approach.md`, etc.).  
- **Iterate** as you discover new requirements or constraints.  
- **Have fun**—this is a challenging but super-rewarding project to see come to life in 3D!

**End of ROADMAP.md**  
