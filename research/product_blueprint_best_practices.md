# Product Blueprint & Global Best Practices

This document defines the "Best-in-Class" technical state for the `neural-nexus` project, mapping the current implementation to global industry standards.

---

## 1. Technical Stack (The "Nexus" Engine)

| Layer                 | Technology                  | Global Standard Role                                                     |
| :-------------------- | :-------------------------- | :----------------------------------------------------------------------- |
| **Backend Framework** | **FastAPI (Python)**        | High-performance, asynchronous REST API.                                 |
| **Primary Graph DB**  | **Neo4j + GDS**             | Industry gold-standard for structural analytics and community discovery. |
| **Relational DB**     | **PostgreSQL (SQLAlchemy)** | ACID-compliant storage for metadata, users, and session folders.         |
| **AI Orchestration**  | **LangGraph / Ollama**      | State-of-the-art agentic RAG for local, private reasoning.               |
| **Frontend Visuals**  | **Three.js / D3.js**        | Premium 3D neural space and 2D force-directed network views.             |
| **Infrastructure**    | **Redis / Alembic**         | High-speed caching and reliable database schema migrations.              |

---

## 2. Infrastructure Best Practices: Folders & DBs

### The "Folder" Philosophy
- **Current State**: Logical partitioning using a `folder_id` property.
- **Global Standard**: **Software-Defined Isolation**.
- **The "Better" Way**: Move from property-based filtering (`{folder_id: 'X'}`) to **Dynamic Labeling** (`:Folder_X`). 
    - *Why?*: This gives you the speed of a separate database while keeping the ability to perform cross-folder insights in the same engine.

### Database Strategy
- **Current State**: Single Multi-Model (SQL + Graph).
- **The "Better" Way**: Keep the single Neo4j instance to maximize GDS performance, but use **Multi-Tenancy** features if scaling to 10,000+ researchers.

---

## 3. Feature-Wise Best Practices (What can be done better?)

### A. Graph Data Science (GDS)
- **Standard**: Zero-approximation analytics.
- **Improvement**: Implement the **Symmetry Guardian**. Never allow `A -> B` and `B -> A` simultaneously for associations. This ensures PageRank scores are 100% mathematically accurate.

### B. AI Reasoning (RAG)
- **Standard**: Contextual Grounding (Bi-directional).
- **Improvement**: Change retrieval from "Forward-Only" to **Undirected Expansion**. The AI should see a herb regardless of whether the arrow points *to* it or *from* it.

### C. Visual Clarity
- **Standard**: Cognitive Load Reduction.
- **Improvement**: Pass a `isSymmetric` flag to the frontend. If a relationship is an association (like "SIMILAR_TO"), **hide the arrow**. Only show arrows for hierarchies (like "PART_OF").

### D. Data Modeling
- **Standard**: Semantic Labeling.
- **Improvement**: Stop using the generic `:Entity` label for everything. Use specific labels like `:Herb`, `:Chemical`, and `:Disease`. 
    - *Impact*: Reduces Cypher search time by up to 90% in large graphs.

---

## 4. Operational "Global Standards" Checklist

- [ ] **Native Schema**: Does the system use Neo4j Labels? (Recommended: YES).
- [ ] **Bi-directional RAG**: Does the AI look both ways? (Recommended: YES).
- [ ] **Symmetry Control**: Is the graph math biased? (Recommended: NO).
- [ ] **Folder Isolation**: Is data strictly scoped? (Recommended: YES).
- [ ] **Visual Distinction**: Are arrows used correctly? (Recommended: YES).

---

**Final Verdict**: The project has a **Tier-1 Tech Stack**. By applying the "Better" improvements listed above, you transform it from a "Highly Functional Prototype" into a **"Commercial-Grade Knowledge Engine."**
