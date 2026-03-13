# Neo4j & Folder Architecture: End-to-End Flow

This document explains the technical implementation of "Folders" and how Neo4j/GDS works in the `neural-nexus` product.

---

## 1. The "Folder" Concept
In `neural-nexus`, a **Folder** is not a physical directory on a disk. It is a **logical namespace** used to group related botanical data (herbs, chemicals, diseases).

- **Implementation**: Every node created in Neo4j has a property called `folder_id`.
- **Purpose**: This allows multiple users or different research projects to co-exist in the same database without their data mixing. It also enables high-performance "Scoped Analytics."

---

## 2. End-to-End Data Flow

### Step 1: Ingestion & Extraction
- When you upload a document or provide text, the system extracts **Entities** (e.g., "Ginseng") and **Relationships** (e.g., "Ginseng CONTAINS Ginsenosides").
- Each of these is tagged with a unique `folder_id`.

### Step 2: Neo4j Storage
- Data is sent to the **Neo4j Graph Database**.
- Nodes are saved with the label `:Entity` and properties like `name`, `type`, and `folder_id`.
- Relationships are saved with types like `:CONTAINS`, `:TREATS`, or `:HAS_EFFECT`.

### Step 3: GDS Projection (The "In-Memory" Graph)
Before any complex math happens, the system uses **Neo4j GDS (Graph Data Science)**.
- The `GDSService` calls `gds.graph.project.cypher`.
- It creates a temporary, in-memory version of the graph **filtered by folder_id**.
- **Result**: Even if the database has 1 million nodes, if your folder only has 100 nodes, the algorithm runs instantly on just those 100.

### Step 4: Analytical Execution
- When you ask "Which phytochemical is most important?", the `AnalyticChatService` triggers the `GDSService`.
- The service runs a GDS algorithm (like **PageRank**) on the folder's in-memory projection.
- The raw scores are returned to the backend.

### Step 5: AI Insight & Visualization
- The backend sends the raw scores to an **LLM** (e.g., Ollama/GPT).
- The LLM turns "Score 0.85" into a human sentence: *"Ginsenoside is highly influential because it connects to 5 different therapeutic uses."*
- Finally, the frontend (**NeuralSpace3D**) highlights these specific nodes, making them pulsate in the graph view.

---

## 3. Advanced Technical Details

### Symmetry & Directionality
As identified in our research, the current flow has a small gap: it primarily respects **Directed** relationships. 
- **The Fix**: The [Remediation Plan](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/remediation/remediation_plan.md) updates Step 3 to use `orientation: 'UNDIRECTED'` in the GDS projection, ensuring that the "Folder" analysis sees connections in both directions.

### Scalability
By using `folder_id` projections, the system stays fast even as more data is added globally. The "In-Memory" strategy ensures that we don't have to scan the entire 20GB database for every single chat message.

## 4. Practical Examples: Works vs. Fails

To understand the system's current boundaries, here are specific examples of what the "Folder + Neo4j" architecture handles today versus where it needs the [Remediation Plan](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/remediation/remediation_plan.md).

### ✅ What Works (Product Strengths)
- **Scenario**: "In Folder 'Chinese Herbology', which phytochemical is most important?"
    - **Logic**: The system instantly projects only the 'Chinese Herbology' data into memory. PageRank runs in milliseconds even if the global DB is massive.
- **Scenario**: "List all herbs in my 'Ayurveda' folder that treat Diabetes."
    - **Logic**: Cypher query `MATCH (n {folder_id: 'ayurveda'})` ensures 100% data isolation. You never see 'Chinese Herbology' nodes by mistake.
- **Scenario**: "Visualize the 3D network for my current research."
    - **Logic**: The frontend fetches only nodes tagged with the active `folder_id`, creating a clean, relevant 3D map.

### ❌ What Fails Today (Needs Remediation)
- **Scenario**: "Which herbs treat Inflammation?" (If the link is `Herb -> Chemical -> Inflammation`)
    - **Logic**: If the RAG or GDS projection is **Directed-only**, and the search starts from the disease, it won't see the "incoming" link from the chemical.
- **Scenario**: "Compare the effectiveness of herbs in Folder A vs. Folder B."
    - **Logic**: GDS projections are currently **Folder-Locked**. The system cannot "bridge" two projections in a single algorithmic run without the [Global Scaling Fix](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/remediation/comprehensive_remediation_summary.md).
- **Scenario**: "Find the shortest path between Herb X and Disease Y."
    - **Logic**: In a directed graph, if the path involves any "against the arrow" steps (e.g., `Herb A -> Chemical <- Herb B`), the search **fails** to find the connection.

---

**Summary**: Your product is excellent at **isolated, high-speed research within a folder**. It currently fails at **complex bi-directional discovery** and **cross-folder comparisons**, which is why the [Detailed Execution Plan](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/remediation/detailed_execution_plan.md) is critical for your next phase.
