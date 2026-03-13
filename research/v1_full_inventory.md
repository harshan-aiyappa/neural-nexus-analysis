# Neural Nexus V1: Full Technical Inventory

This document provides a comprehensive list of all features, pages, components, and functionalities of Version 1.0 of Neural Nexus. This serves as the reference architecture for Version 2.0.

---

## 📅 Version Info
- **Version**: 1.0.x
- **Release Date**: March 2026
- **Stability**: Production-grade Research Platform

---

## 🖥️ Frontend Inventory (`neural-nexus-fe`)

### 📄 Pages (App Router)
- **`/` (Landing Page)**: Hero section, value propositions, and entry points.
- **`/login` / `/signup`**: User authentication and onboarding.
- **`/dashboard`**: Main control center with system metrics and recent activities.
    - **`/dashboard/audit`**: Secure logs of system operations and graph changes.
- **`/library`**: Document management system. View uploaded files, status of ingestion, and metadata.
- **`/folders`**: Workspace exploration.
    - **`/folders/[folderId]`**: Specific workspace view containing filtered document sets and dedicated analytics.
- **`/graph`**: The primary 2D/3D visualization workspace for interactive node exploration.

### 🧩 Core Components
- **Visualization Hub**:
    - **`DataCanvas`**: The engine for rendering massive graph structures.
    - **`GraphContainer`**: Orchestrates 2D/3D physics (D3/Three.js).
    - **`VisualizationGallery`**: Entry point for all specialized charts.
- **Hierarchical Charts**: Sunburst, Treemap, Circle Pack.
- **Flow Charts**: Sankey and Chord diagrams.
- **Interactive Layers**:
    - **`CommandPalette`**: (⌘K) Global search and command execution.
    - **`EntityInfoPanel`**: Side panel for detailed node/edge metadata.
    - **`AlgorithmPicker`**: Interface for selecting and running GDS algorithms.
- **Layout & Shell**: Sidebar navigation, Theme switching, and Toast notifications.

### ⚡ Client-side Functionalities
- **Real-time Synchronization**: Listening to SSE/WebSockets for ingestion progress.
- **Dynamic Physics Controls**: Toggling layout forces, link directions, and node collision.
- **Theme Engine**: Support for premium "Jungle Teal" aesthetics and Dark/Light modes.
- **Interactive RAG Chat**: Floating chat interface for querying the graph in natural language.

---

## ⚙️ Backend Inventory (`neural-nexus-be`)

### 🛣️ API Routes (Routers)
- **`auth.py`**: User registration, JWT login, session management.
- **`folders.py`**: Hierarchical workspace creation and CRUD operations.
- **`files.py`**: Metadata management for uploaded documents.
- **`upload.py`**: Multi-stage ingestion pipeline (Upload -> Parse -> Extract -> Embed -> Insert).
- **`graph.py`**: Cypher query generation, neighborhood discovery, and visualization data streaming.
- **`analytics.py`**: Statistical aggregation and graph metrics.
- **`gds_service.py`**: Integration with Neo4j Graph Data Science for PageRank, Louvain, etc.
- **`query.py`**: Vector search and LLM-driven RAG implementation.
- **`reasoning.py`**: Multi-hop path analysis and advanced agentic logic.
- **`browse.py`**: Efficient data browsing across folders.
- **`analytics_chat.py`**: Specialized AI agent for answering data-specific mathematical questions.

### 🧠 Core Services & Business Logic
- **Graph Extraction Engine**: LLM-based entity and relationship recognition from raw text.
- **Vector Manager**: Handling embeddings for FAISS/Chroma integration.
- **GDS Runner**: Orchestrating in-memory projections and algorithm execution.
- **Task Processor**: Background handling of heavy ingestion tasks.
- **Neo4j Utils**: High-performance Cypher execution for `MERGE` and hierarchical filtering.

---

## 🗄️ Database Schema & Models

### Neo4j (Graph)
- **Nodes**: `Herb`, `Chemical`, `Effect`, `Illness`, `Source`, `Folder`.
- **Relationships**: `CONTAINS`, `TREATS`, `CAUSES`, `BELONGS_TO`, `EXTRACTED_FROM`.
- **Logic Scoping**: All nodes and relationships tagged with `folder_id` for isolation.

### PostgreSQL (Relational)
- **`Users`**: Credentials and profiles.
- **`Folders`**: Workspace metadata and hierarchy.
- **`Files`**: Document metadata, upload status, and processing logs.
- **`AuditLogs`**: Detailed tracking of all user-driven data modifications.

---

## 🚀 Key V1 Capabilities SUMMARY
1. **Isolated Workspaces**: Complete data separation using logical folders.
2. **Specialized Extraction**: Fine-tuned for botanical and medical domain knowledge.
3. **Advanced Visuals**: Native 2D/3D graph visualization with interactive node panels.
4. **Agentic RAG**: Direct natural language interaction with the graph schema.
5. **GDS Integration**: Enterprise-grade math (centrality, community) on graph sub-projections.
