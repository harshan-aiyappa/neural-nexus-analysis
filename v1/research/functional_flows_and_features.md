# Neural Nexus: Functional Flows & Features Guide

This document breaks down every major feature and functional flow currently present in the `neural-nexus` platform. It serves as the step-by-step technical mapping of how the system operates under the hood.

---

## 1. Feature: Document Ingestion & Graph Extraction
**Objective**: Turn flat PDF text into a connected knowledge graph.
*   **Step 1: Upload**: User uploads a document via the React Frontend.
*   **Step 2: Parsing**: The Backend (`pdf_service.py`) extracts raw text.
*   **Step 3: Chunking**: Text is split into semantic chunks.
*   **Step 4: AI Extraction**: Langchain sends chunks to the LLM (Ollama) with a prompt to extract specialized Entities (Herb, Chemical) and Relationships (TREATS, CONTAINS).
*   **Step 5: Database MERGE**: The `neo4j_utils.py` executes Cypher commands to `MERGE` nodes and edges into Neo4j, tagging every item with the current `folder_id`.

---

## 2. Feature: Graph Data Science (GDS) Analytics
**Objective**: Run complex mathematical algorithms on a specific folder's data.
*   **Step 1: Request**: User requests PageRank (importance) or Louvain (communities) analytics on "Folder A".
*   **Step 2: Projection Setup**: The Backend (`gds_service.py`) checks if an in-memory graph projection exists for "Folder A". If not, it creates a temporary, undirected RAM projection of the graph.
*   **Step 3: Algorithm Execution**: The GDS engine runs the requested algorithm rapidly on the RAM projection.
*   **Step 4: Stream & Write**: The scores are either streamed directly to the frontend table or written back to the Neo4j nodes as a property (e.g., `n.pagerank_score`).

---

## 3. Feature: Agentic Retrieval-Augmented Generation (RAG)
**Objective**: Allow natural language chat with the knowledge graph.
*   **Step 1: User Prompt**: User asks, "What is the most central chemical in this folder?"
*   **Step 2: Vector Search**: The system converts the question to vector embeddings and finds semantically relevant graph nodes.
*   **Step 3: Cypher Generation**: LangGraph generates an exact Cypher query based on the question syntax.
*   **Step 4: Graph Execution**: The query executes against Neo4j, pulling data explicitly filtered by `folder_id`.
*   **Step 5: AI Synthesis**: The raw JSON graph data is fed back to the LLM. The LLM summarizes it into human-readable text and returns it to the user.

---

## 4. Feature: Visualization Engine (2D/3D & Interaction)
**Objective**: Visually represent complex biological connections in real-time.
*   **Step 1: Data Fetch**: The frontend fetches a clustered subset of the Neo4j graph data.
*   **Step 2: 2D/3D Toggle Strategy**: 
    *   **3D Mode**: Uses `react-three-fiber` and `d3-force-3d` for immersive, physics-based spatial exploration.
    *   **2D Mode**: Uses standard 2D physics for clearer, flat hierarchy mapping and readability.
*   **Step 3: Graph Controls (UI)**: The `GraphContainer` components allow zooming, panning, pausing physics, and highlighting specific nodes.
*   **Step 4: Metadata Panels**: The user clicks a node (e.g., a specific chemical). The frontend pulls the underlying Neo4j properties and opens the `EntityInfoPanel` or `AnalyticsPanel` on the side.

---

## 5. Feature: Advanced UI Tooling (Editor, Compare, Export)
**Objective**: Provide professional-grade workspace tools for researchers.
*   **Command Palette (⌘K)**: A globally accessible quick-search bar allowing users to jump directly to specific herbs, chemicals, or UI actions without manual navigation.
*   **Graph Comparison Tool**: Allows users to load two different subgraph projections side-by-side to visually identify differences in relationships or community clusters.
*   **Node/Schema Editor**: A manual UI tool allowing authorized users to create, delete, or modify nodes and relationship edges directly from the visualization space.
*   **Data Export**: Users can serialize their current view (or specific GDS results) and export it to JSON or CSV for use in external analytical tools.

---

## 6. Feature: Logical "Folder" Workspaces
**Objective**: Keep multi-tenant or multi-project data isolated in one physical database.
*   **Step 1: Session Creation**: A new folder record is created in PostgreSQL.
*   **Step 2: Tagging**: All subsequent Neo4j ingestions append the `folder_id` property.
*   **Step 3: Filtering**: Every analytical read operation or LLM context retrieval includes `WHERE n.folder_id = 'XYZ'`. *(Note: Pending upgrade to Native Labels for enhanced performance).*

---

## Technical Appendix: How It All Connects Today
Currently, the **Frontend (React)** is the "Controller," heavily relying on the **Backend (FastAPI)** to orchestrate heavy lifting. When a user clicks "Run PageRank" via the UI `Algorithm Picker`, the frontend sends a REST request to FastAPI. FastAPI uses the `folder_id` to generate an in-memory graph projection on **Neo4j GDS**, runs the math, and returns the top 10 nodes to FastAPI, which then forwards them to the React frontend for visual highlighting (e.g., turning those specific 3D spheres bright red).
