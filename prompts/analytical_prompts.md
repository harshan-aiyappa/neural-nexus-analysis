# Analytical Prompts: Status & Handling Strategy

This document tracks the capabilities of the `neural-nexus` graph engine in answering your 15 specific analytical questions.

## Prompt Status Summary

| #    | Analytical Prompt                   | Implementation Handler | Status | Reliability Note                                                       |
| :--- | :---------------------------------- | :--------------------- | :----: | :--------------------------------------------------------------------- |
| 1    | Most important phytochemicals?      | GDS PageRank           |   ✅    | **WORKS**: High reliability for node ranking.                          |
| 2    | Herbs for inflammation?             | RAG / Cypher Match     |   ⚠️    | **NEEDS WORK**: Requires undirected RAG expansion to be 100% accurate. |
| 3    | Groups for similar diseases?        | GDS Louvain            |   ✅    | **WORKS**: Identifies structural communities.                          |
| 4    | Uses for important herbs?           | GDS PageRank + Degree  |   ✅    | **WORKS**: Hub detection.                                              |
| 5    | Herb with widest range of benefits? | GDS Degree             |   ✅    | **WORKS**: Connectivity count.                                         |
| 6    | Parts used together?                | GDS Node Similarity    |   ✅    | **WORKS**: Jaccard similarity.                                         |
| 7    | Phytochemicals connecting herbs?    | GDS BFS / Path         |   ⚠️    | **NEEDS WORK**: Biased if symmetric links are missing/duplicated.      |
| 8    | Similar chemical structures?        | GDS Node Similarity    |   ✅    | **WORKS**: Neighbor-based similarity.                                  |
| 9    | Common plant parts?                 | GDS Degree             |   ✅    | **WORKS**: Frequency analysis.                                         |
| 10   | Highest influence phytochemical?    | GDS ArticleRank / HITS |   ✅    | **WORKS**: Advanced recursive ranking.                                 |
| 11   | Disease with most chemicals?        | GDS Degree             |   ✅    | **WORKS**: In-degree counting.                                         |
| 12   | Herbs with similar chemicals?       | GDS Node Similarity    |   ⚠️    | **NEEDS WORK**: Accuracy depends on "Symmetry Guardian" fix.           |
| 13   | Parts for similar treatments?       | GDS Node Similarity    |   ✅    | **WORKS**: Shared neighbor analysis.                                   |
| 14   | Herb treats most diseases?          | GDS Degree             |   ✅    | **WORKS**: Connectivity hub detection.                                 |
| 15   | Clusters of diseases?               | GDS Louvain            |   ✅    | **WORKS**: Global community clustering.                                |

---

## Technical Handling Logic

### 1. Influence & Importance (PageRank/ArticleRank)
- **Logic**: Importance in a graph is best measured by recursive connectivity.
- **GDS Call**: `CALL gds.pageRank.stream($graph, {target_type: "Phytochemical"})`

### 2. Connectivity & Hubs (Degree Centrality)
- **Logic**: Direct connection counts identify nodes that act as major hubs (e.g., Herbs treating multiple diseases).
- **GDS Call**: `CALL gds.degree.stream($graph)`

### 3. Similarity & Structure (Node Similarity)
- **Logic**: Jaccard similarity measures overlap in neighbors (e.g., two herbs sharing many phytochemicals).
- **GDS Call**: `CALL gds.nodeSimilarity.stream($graph, {topK: 10, similarityCutoff: 0.1})`

### 4. Clustering & Groups (Louvain/Leiden)
- **Logic**: Community detection identifies "islands" of related entities more connected to each other than the rest of the graph.
- **GDS Call**: `CALL gds.louvain.stream($graph)`

---

## Expected Results Format
Each response in the AI Chat will include:
1. **Natural Language Answer**: Interpreted by the LLM.
2. **Algorithm Insight**: Description of why certain nodes were ranked higher.
3. **Graph Results**: Top 10 entities/groups with their scores.
4. **Visual Highlight**: Selected nodes will pulsate in the `NeuralSpace3D` frontend.
