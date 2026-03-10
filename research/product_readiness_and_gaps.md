# Product Readiness & Gaps Report

This document serves as the master authority on the `neural-nexus` product's current capabilities, identified problems, and the roadmap for full resolution of all global edge cases.

---

## 1. Feature Capability Matrix

| Feature Area        | Sub-Feature         | Status | Capability / Handled Logic                                                   |
| :------------------ | :------------------ | :----: | :--------------------------------------------------------------------------- |
| **Graph Analytics** | Structural Hubs     |   ✅    | GDS Degree Centrality for identifying key herbs/diseases.                    |
|                     | Global Influence    |   ✅    | GDS PageRank/ArticleRank for phytochemical importance.                       |
|                     | Community Discovery |   ✅    | GDS Louvain/Leiden for botanical/therapeutic clustering.                     |
|                     | Similarity Scoring  |   ✅    | Jaccard Similarity for herb-profile comparisons.                             |
| **AI Chat & RAG**   | General Querying    |   ✅    | Semantic node retrieval via Ollama/LangGraph.                                |
|                     | Analytical Querying |   ✅    | Natural Language mapping to GDS algorithm execution.                         |
|                     | Multi-hop Context   |   ⚠️    | Context expansion is currently directed-only (incoming edges missed).        |
| **Visualization**   | 2D Force-Graph      |   ✅    | Interactive D3 network exploration with labels.                              |
|                     | 3D Neural Space     |   ✅    | Immersive clustered visualization using Three.js.                            |
|                     | Link Direction      |   ❌    | Visual ambiguity: all links show arrows (even symmetric ones).               |
| **Data Integrity**  | Folder Scoping      |   ✅    | Analysis isolation via `folder_id` projections.                              |
|                     | Symmetry Control    |   ❌    | No "Symmetry Guardian": risk of biased scoring from duplicate inverse links. |

---

## 2. Global Problems & Critical Edge Cases

### The "Symmetry Bias" (Critical)
- **Problem**: The system treats `A -> B` and `B -> A` as separate entities for symmetric relationships.
- **Global Impact**: Skews all statistical and analytical reports (centrality, clustering, weights).
- **Status**: **NOT DONE** (Proposed fix in Remediation Plan).

### The "Forward-Only" RAG (Major)
- **Problem**: AI reasoning primarily follows outgoing relationship arrows.
- **Global Impact**: Missing "upstream" context. If searching for connections to a disease, the system might miss the chemical that treats it if the link points from chemical to disease.
- **Status**: **NOT DONE** (Proposed fix in Remediation Plan).

### The "Aesthetic Ambiguity" (Minor/UX)
- **Problem**: Inconsistent arrow rendering in Frontend.
- **Global Impact**: Leads to user confusion about hierarchy vs. association.
- **Status**: **NOT DONE** (Proposed fix in Remediation Plan).

---

## 3. Problems vs. Solutions Deep Dive

| Problem Case                | Technical Solution (Safe)                                                                  | File(s) to Modify          |
| :-------------------------- | :----------------------------------------------------------------------------------------- | :------------------------- |
| **Duplicate Inverse Links** | **Symmetry Guardian**: Whitelist symmetric types and check inverse existence before merge. | `neo4j_utils.py`           |
| **Incomplete RAG Context**  | **Undirected Retrieval**: Change Cypher patterns to `(n)-[r]-(m)` for keyword expansion.   | `enhanced_rag.py`          |
| **Misleading Link Arrows**  | **Conditional Markers**: Hide arrows based on `SYMMETRIC_TYPES` constant.                  | `ForceGraph2D.tsx`         |
| **Slow Deep Queries**       | **Label Migration**: Use native `:Herb` / `:Phytochemical` labels for indexed lookup.      | `analytics.py`             |
| **Timeout on 5+ Hops**      | **GDS Native Fallback**: Use in-memory pathfinding for complex discoveries.                | `analytic_chat_service.py` |

---

## 4. Final Verdict: Done vs. Not Done

### ✅ What is 100% Reliable (Done)
- The analytical chat engine successfully executes PageRank, Degree, and Clustering.
- The 15 prompts derived from your request are correctly mapped to algorithms.
- Folder-based isolation works as designed.

### ❌ What Requires Action (Not Done)
- **Code implementation of Safe Fixes**: The logic is documented, but the code has not been modified to respect your "Analysis Only" instruction.
- **Schema Hardening**: Moving from generic labels to specific ones to support large-scale production load.
- **Visual Accuracy**: Fine-tuning the 2D view to distinguish associations from hierarchies.

*For specific implementation steps, refer to the [Detailed Execution Plan](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/remediation/detailed_execution_plan.md).*
