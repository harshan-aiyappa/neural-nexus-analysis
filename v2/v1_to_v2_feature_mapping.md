# Neural Nexus: V1 to V2 Feature Mapping & Evolution

This document maps the existing Version 1 (V1) features to their planned evolution in Version 2 (V2). It serves as a side-by-side reference for development.

---

## 🏛️ Core Architecture Evolution

| Feature Area | V1 Implementation (Current) | V2 Proposed Evolution | V1 Reference |
| :--- | :--- | :--- | :--- |
| **Logic Scoping** | **Property-based**: `folder_id` on every node/edge. | **Native Labels**: `:Folder_ID` labels for instant $O(1)$ lookup. | [V1 Inventory](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/v1/research/v1_full_inventory.md#🗄️-database-schema--models) |
| **Data Integrity** | **Unconstrained**: Possible duplicate/inverse relationships. | **Symmetry Guardian**: Middleware to ensure mathematical graph perfection. | [V1 Failures](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/v1/research/critical_failure_analysis.md) |
| **GDS Analytics** | **Standard Projections**: PageRank, Louvain on properties. | **Advanced GDS**: Multi-modal projections & custom algo tuning. | [V1 Flows](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/v1/research/functional_flows_and_features.md#2-feature-graph-data-science-gds-analytics) |

---

## 🖥️ Frontend & UX Transition

| Page / Component | V1 Functionality | V2 Improvement Goals | V1 Reference |
| :--- | :--- | :--- | :--- |
| **Graph View** | 2D/3D toggle with basic physics. | Premium **GSAP** animations, smoother 3D transitions, better depth perception. | [V1 Visuals](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/v1/research/functional_flows_and_features.md#4-feature-visualization-engine-2d3d--interaction) |
| **Dashboard** | Static metrics and audit logs. | **Dynamic Analytics**: Real-time trend charts and health monitoring. | [V1 Inventory](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/v1/research/v1_full_inventory.md#📄-pages-app-router) |
| **RAG Chat** | Functional LangGraph-based chat. | **Agentic 2.0**: Better multi-hop reasoning and citation accuracy. | [V1 RAG](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/v1/research/functional_flows_and_features.md#3-feature-agentic-retrieval-augmented-generation-rag) |

---

## ⚙️ Backend & API Strategy

| Service / Logic | V1 Approach | V2 Approach | V1 Reference |
| :--- | :--- | :--- | :--- |
| **Extraction** | LLM-based entity/relationship extraction. | **Fine-tuned Extraction**: Support for more complex schemas and edge cases. | [V1 Ingestion](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/v1/research/functional_flows_and_features.md#1-feature-document-ingestion--graph-extraction) |
| **Auth & Security** | Standard JWT. | **Enhanced Auth**: Role-based access control (RBAC) for folders. | [V1 Inventory](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/v1/research/v1_full_inventory.md#🛣️-api-routes-routers) |

---

## 📑 Implementation Reference
When building V2 components, always consult the **[V1 Full Inventory](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/v1/research/v1_full_inventory.md)** to ensure legacy features are either maintained or explicitly deprecated.
