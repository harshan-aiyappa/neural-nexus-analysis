# 🏆 Neural Nexus V2: Master Manifest & Feature Inventory

This document serves as the absolute source of truth for the **Neural Nexus V2** project. It details the completed architecture, active features, access points, and the status of all development phases.

---

## 🏛️ V2 Project Status (Phases)

| Phase | Title | Status | Primary Capability Delivered |
| :--- | :--- | :--- | :--- |
| **0** | **Infrastructure Setup** | ✅ **DONE** | Redis/Docker, Master IP (10.10.20.122) connectivity. |
| **1** | **Symmetry Guardian** | ✅ **DONE** | Cross-DB integrity (Neo4j <> MongoDB) & Atomic folder deletion. |
| **2** | **Undirected RAG** | ✅ **DONE** | Multi-hop scientific reasoning & Gemini-powered query expansion. |
| **19** | **Advanced GDS & Async** | ✅ **DONE** | Redis-cached analytics & Celery background workers. |
| **20** | **Dev Hardening** | ✅ **DONE** | Hardened Identity (JWT) & standardized Dev startup routine. |

---

## 🚀 Feature Inventory (V2 Core)

### 1. **Knowledge Intelligence (RAG 2.0)**
- **Multi-Hop Discovery**: AI can trace relationships across multiple nodes (e.g., Herb -> Compound -> Target).
- **Semantically Aware**: Automatically expands user scientific queries into technical synonyms before searching.
- **Bi-Directional Awareness**: Navigates scientific facts regardless of link direction in the graph.

### 2. **Performance Architecture**
- **Symmetry Guardian Middleware**: Automatically syncs MongoDB metadata (like `node_count`) with Neo4j state.
- **GDS Cache (Redis)**: Community, PageRank, and Similarity results are cached for sub-second retrieval.
- **Async Ingestion**: Heavy data extraction and Cypher loads are handled in the background via Celery.

### 3. **System Integrity & Control**
- **Health Dashboard**: Real-time status monitoring of Neo4j, MongoDB, Redis, and Gemini API.
- **Settings Registry**: Unified API key validation and SDK management (migrated to `google.genai`).
- **Scoped Isolation**: High-speed folder indexing using native Neo4j labels (`:Folder_<ID>`).

---

## 🔐 Access & Development Registry

### **Core Services (Development Environment)**
- **Main Entry (UI)**: [http://10.10.20.122:5173/](http://10.10.20.122:5173/)
- **API Backend**: [http://10.10.20.122:8000/](http://10.10.20.122:8000/)
- **API Docs (Swagger)**: [http://10.10.20.122:8000/docs](http://10.10.20.122:8000/docs)
- **Settings Status**: [http://10.10.20.122:8000/api/settings/status](http://10.10.20.122:8000/api/settings/status)

### **Infrastructure (Static IP: 10.10.20.122)**
- **Neo4j**: Bolt (`7687`), Browser (`7474`)
- **MongoDB**: Connection (`27017`)
- **Redis/Celery**: Connection (`6379`)

---

## 📂 Project Organization (New Structure)
- **`data/`**: Scopes and raw datasets (`Herb modelling example data.xlsx`, `bioactives_kg.cypher`).
- **`docs/`**: System architecture and comparison reports (`architecture_overview.md`, `backend_comparison_report.md`).
- **`archives/`**: Legacy ZIPs and backup folder structures.
- **`analysis/`**: Deep-dive feature inventories and mapping.

---

### 📑 How to Start V2 (Full Stack)
Consult the **[HOW_TO_START_V2.md](file:///d:/01_Projects/OpenSource/neural-nexus/Version%20V2/HOW_TO_START_V2.md)** for detailed startup commands for Backend, Frontend, and Celery.
