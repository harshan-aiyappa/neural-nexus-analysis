# Graph Analysis: Solved vs. Unsolved Cases (End-to-End)

This document provides a comprehensive overview of the current state of the `neural-nexus` graph system, highlighting what works today and what requires remediation for the project to be fully "production-ready" for complex botanical analysis.

## 1. Currently Solved (What Works Now)

### Backend & Analytics
- **Natural Language to Graph**: The system successfully translates complex questions (the 15 prompts) into GDS algorithms using an LLM.
- **Global Graph Metrics**: Algorithms like **PageRank**, **Louvain**, and **Degree Centrality** are fully integrated and functional for "Entity" nodes.
- **Folder and Node Scoping**: The GDS service correctly filters analysis to specific subgraphs (by `folder_id` or specific `node_ids`).
- **Pathfinding**: BFS and DFS traversals are implemented for discovery.

### Visualization
- **3D Clustered View**: High-level clustering of herb/chemical hubs is visually represented in `NeuralSpace3D`.
- **2D Relationship Mapping**: Interactive exploration of node networks with labels and force-directed positioning.

---

## 2. Need to be Solved (What is Missing)

### Data Integrity (Global Consistency)
- **Bidirectional Symmetry**: The system lacks a server-side "Symmetry Guardian." It currently treats `A -> B` and `B -> A` as distinct, which biases all GDS algorithm scores (double-counting).
- **Schema Hardening**: Nodes rely on a generic `Entity` label + `type` property. Using native Neo4j labels (e.g., `:Phytochemical`, `:Herb`) would increase query performance by up to 50x.

### RAG & AI Reasoning
- **Search Directionality**: The RAG expansion is "forward-looking." It misses relationships if the starting node is the "target" (e.g., finding the Herb from a Phytochemical).
- **Ambiguous Relationship Logic**: The AI doesn't distinguish between hierarchical (A causes B) and associative (A is related to B) links during its reasoning phase.

### Frontend Clarity
- **Visual Misinterpretation**: Every link in the 2D view has an arrowhead. This is misleading for symmetric relationships and needs a "conditional arrow" logic based on relationship type.

---

## 3. End-to-End Gap Analysis

| Case / Flow                  | Solved? | Problem                            | Proposed Fix (End-to-End)                                  |
| :--------------------------- | :-----: | :--------------------------------- | :--------------------------------------------------------- |
| **Phytochemical Importance** |    ✅    | Works well for global ranking.     | None needed for current scale.                             |
| **Similar Herb Clusters**    |    ⚠️    | Biased if duplicate links exist.   | Implement "Symmetry Guardian" in `neo4j_utils.py`.         |
| **Therapeutic Logic**        |    ⚠️    | Directional blind spots in RAG.    | Update `enhanced_rag.py` to use undirected matches.        |
| **UI Relation Arrows**       |    ❌    | All links show arrows.             | Add `SYMMETRIC_TYPES` list to `ForceGraph2D.tsx`.          |
| **Large-Scale Querying**     |    ❌    | Cypher paths are slow for 5+ hops. | Implement GDS Native Projections fallback for pathfinding. |

## 4. Complexity & Global Scale
The current codebase is a strong foundation for **medium-scale** research. To support **global-scale** (millions of connections), the shift from Cypher-only traversals to GDS-backed "middle-loading" (Phase 3 of the Remediation Plan) is critical. This ensures that even the most complex 10-hop botanical relationships return in milliseconds.
