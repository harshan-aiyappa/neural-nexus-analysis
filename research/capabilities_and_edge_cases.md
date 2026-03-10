# Project Capabilities & Edge Case Analysis

This document summarizes the current analytical capabilities and identified problems (edge cases) within the `neural-nexus` Neo4j implementation.

## 1. System Capabilities (What is Handled)

### Analytical Query Engine
The system uses `AnalyticChatService` to translate natural language into Graph Data Science (GDS) algorithms:
- **Influence Ranking**: Handled via **PageRank/ArticleRank** (e.g., finding the most important phytochemicals).
- **Cluster Identification**: Handled via **Louvain/Leiden** (e.g., grouped therapeutic uses).
- **Similarity Comparison**: Handled via **Jaccard Similarity** (e.g., finding herbs with similar chemical profiles).
- **Structural Integrity**: Handled via **Degree Centrality** and **Triangle Counting** (e.g., identifying the most researched plant parts).

### Graph Visualization
- **2D Exploration**: `ForceGraph2D` provides an interactive force-directed layout with pan/zoom and relationship labels.
- **3D Immersive View**: `NeuralSpace3D` offers a clustered, hub-based visualization using Three.js for deep graph intuition.
- **Contextual Filtering**: The UI supports "Herb-centric" BFS (Breadth-First Search) to highlight specific relationship chains (e.g., `Herb -> Property -> Quality`).

### RAG & Reasoning
- **Enhanced RAG**: Orchestrates document retrieval with graph context.
- **Link Prediction**: Uses GDS to suggest missing relationships in the knowledge graph.
- **Graph Expansion**: Dynamically fetches related node profiles to ground AI answers.

---

## 2. Existing Problems & Edge Cases (What is NOT fully handled)

### Directed vs. Bidirectional Logic
- **Problem**: The system models data with directed relationships, but lacks explicit "bidirectional" constraints.
- **Risk**: Creating both `A -> B` and `B -> A` for a symmetric concept (like "Partner") doubles relationship weights, biasing PageRank and clustering results.
- **UI Issue**: All links in the 2D view have arrows. This misleadingly implies a "flow" or "hierarchy" for purely associative relationships.

### RAG Traversal Blind Spots
- **Problem**: The `enhanced_rag.py` logic primarily traverses *outgoing* edges. 
- **Risk**: If a user asks "Which herbs contain Chemical X?", but the relationship is stored as `Herb -> CONTAINS -> Chemical`, a directed-only search starting from "Chemical X" will fail to find the Herb.

### Java Traversal Framework Absence
- **Problem**: The project does not implement the Neo4j Java Traversal API referenced in the PDF docs.
- **Risk**: Complex, depth-restricted pathfinding with "middle-meeting" collision detection is harder to optimize in pure Cypher for extremely large graphs (millions of nodes).

### Data Quality & Symmetry
- **Problem**: No server-side "Symmetry Guardian" script exists to enforce consistent relationship modeling.
- **Risk**: Inconsistent data entry (e.g., sometimes `Herb -> Treats -> Disease` and sometimes `Disease -> TreatedBy -> Herb`) fragments the graph and breaks analytical queries.
