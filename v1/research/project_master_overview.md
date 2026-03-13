# Neural Nexus: Project Master Overview

This document serves as the comprehensive "End-to-End" master plan and overview of the existing `neural-nexus` project. It details the exact structure, components, and current state of the entire ecosystem.

---

## 1. Project Ecosystem & Structure

The `neural-nexus` ecosystem is divided into three primary repositories/directories:

### A. The Backend (`neural-nexus-be`)
The core reasoning and data processing engine.
*   **Framework**: FastAPI (Python).
*   **Database Integration**: Neo4j (Graph storage & GDS algorithms) + PostgreSQL (Metadata & Relational storage).
*   **AI Integration**: Langchain/LangGraph orchestration using local LLMs (Ollama) and Vector Search (FAISS/Chroma).
*   **Key Responsibilities**:
    *   Ingesting raw botanical/medical documents.
    *   Extracting Graph schema (Entities and Relationships) using LLMs.
    *   Executing Graph Data Science (GDS) projections (PageRank, Community Detection) scoped by `folder_id`.
    *   Serving Agentic RAG chat responses.

### B. The Frontend (`neural-nexus-fe`)
The user-facing visualization and interaction layer.
*   **Framework**: React (Vite).
*   **3D Visualization**: `react-three-fiber` mapped to NeoVis/D3 concepts for immersive graph navigation.
*   **State Management**: Zustand / Context API (assumed).
*   **Styling**: TailwindCSS with premium corporate aesthetics.
*   **Key Responsibilities**:
    *   Providing the interface for file uploads and folder creation.
    *   Rendering massive graph structures in an intuitive 2D/3D space.
    *   Interactive analytical chat interface.

### C. The Analysis Hub (`analysis`)
The strategic documentation and research repository (You are here).
*   **Purpose**: To document exact capabilities, edge cases, system infographics, and global standards for scaling.
*   **Key Artifacts**: Actionable remediation plans, critical failure analysis, and single-database best practices.

---

## 2. Current State vs. Future State Target

| Domain            | What We Have Currently                                                    | Future State Target (Global Standard)                              |
| :---------------- | :------------------------------------------------------------------------ | :----------------------------------------------------------------- |
| **Logic Scoping** | **Property-based (`folder_id`)**: Functional but scans the whole DB.      | **Native Labeling**: `:Folder_ID` for instant O(1) retrieval.      |
| **Integrity**     | **Unconstrained**: Allows duplicate inverse relationships (Biased logic). | **Symmetry Guardian**: Guaranteed mathematical perfection for GDS. |
| **Discovery**     | **Directed Paths**: Hard stops on inward-pointing arrows.                 | **Undirected Traversal**: AI sees the complete "Knowledge Web."    |
| **Visuals**       | **Uniform Arrows**: Everything looks like a dependency.                   | **Hierarchical Clarity**: Arrows only on true directional data.    |

---

## 3. Technology Inventory Details

*   **Database**: Neo4j Graph Database, Neo4j Graph Data Science (GDS) library, PostgreSQL (via SQLAlchemy).
*   **Backend Stack**: Python 3.10+, FastAPI, Uvicorn, LangGraph, Langchain, PyMuPDF (Text Extraction), SentenceTransformers (Embeddings).
*   **Frontend Stack**: React 18, Vite, Three.js, Tailwind CSS, Axios.
*   **Infrastructure**: Redis (Task Caching), Docker (Containerization).

---

## 4. End-to-End Objective
To serve as a self-hosted, highly secure, deeply analytical "Knowledge Engine" for scientific and botanical researchers, allowing them to upload private PDFs and instantly query the complex web of chemicals, illnesses, and biological entities.
