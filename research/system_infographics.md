# Neural Nexus: System Architecture & Data Flow Infographics

This document provides a high-definition detailed breakdown of the `neural-nexus` product architecture, data flow, and technology stack.

---

## 1. High-Definition Visual Blueprint (Light Theme)

Below is a carousel of professional architectural infographics for the platform.

````carousel
![System Architecture Overview](./assets/nexus_system_architecture_light_png_1773129807528.png)
<!-- slide -->
![End-to-End Data Flow Process](./assets/nexus_data_flow_process_light_png_1773129822434.png)
<!-- slide -->
![Tech Stack & Folder Scoping Logic](./assets/nexus_tech_stack_folders_light_png_1773129846432.png)
````

---

## 2. Technical Diagrams (Mermaid)

### 2.1 High-Level Architecture
```mermaid
graph TD
    subgraph "Frontend (React / Three.js)"
        UI[User Interface]
        VG[2D/3D Viz Engine]
        CH[Chat Interface]
    end

    subgraph "Backend (FastAPI)"
        API[REST API Layer]
        AN[Analytics Service]
        RAG[RAG Logic / LangGraph]
        ENC[Encounter Service]
    end

    subgraph "Storage Layer"
        PG[(PostgreSQL)]
        NEO[(Neo4j + GDS)]
        VEC[Vector Store / FAISS]
    end

    UI --> API
    API --> AN
    API --> RAG
    API --> ENC
    AN --> NEO
    RAG --> VEC
    RAG --> NEO
    ENC --> PG
    NEO --> VG
```

### 2.2 End-to-End Logic Flow
```mermaid
sequenceDiagram
    participant U as User
    participant BE as Backend
    participant LLM as LLM (Ollama)
    participant DB as Neo4j Graph
    participant GDS as GDS In-Memory

    U->>BE: Upload Research/Query
    BE->>LLM: Extract Entities & Relations
    LLM-->>BE: JSON Data (Herb, Chemical, etc.)
    BE->>DB: MERGE (with folder_id)
    U->>BE: Ask Analytical Question
    BE->>GDS: Project Sub-graph (Folder_ID)
    GDS->>GDS: Run Algorithm (PageRank/Louvain)
    GDS-->>BE: Raw Scores
    BE->>LLM: Interpret Results
    LLM-->>BE: Human Insight
    BE-->>U: Final Insight + Visual Highlight
```

---

## 3. Technology Stack Breakdown
*The "Nexus Master Engine" Components.*

| Category               | Component              | Usage in Neural Nexus                                      |
| :--------------------- | :--------------------- | :--------------------------------------------------------- |
| **Logic**              | **FastAPI / Python**   | Core API handling and business logic threading.            |
| **Graph Intelligence** | **Neo4j / GDS**        | Storage of relationships and execution of math algorithms. |
| **Relational Storage** | **PostgreSQL**         | Metadata, session handling, and folder registries.         |
| **AI Brain**           | **LangGraph / Ollama** | State-managed agentic RAG for local reasoning.             |
| **Visual Core**        | **React-Three-Fiber**  | Immersive 3D structural visualization.                     |
| **Communication**      | **Redis / WebSockets** | Real-time updates and high-speed data caching.             |

---

**Theme Note**: This documentation and related images are optimized for light-mode viewing to ensure maximum clarity and professionalism.
