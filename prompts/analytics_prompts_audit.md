# Analytics Chat: Prompt Reliability Audit

This document provides a "Success vs. Failure" audit for the 15 analytical prompts when executed through the `AnalyticChatService`.

---

## 🟢 Guaranteed Success (Works Now)
These prompts use single-algorithm GDS queries that are already fully implemented and verified.

| #      | Prompt                              | Why it works                                                                      |
| :----- | :---------------------------------- | :-------------------------------------------------------------------------------- |
| **1**  | Most important phytochemicals?      | **PageRank** is implemented and maps correctly to the `Phytochemical` type.       |
| **5**  | Herb with widest range of benefits? | **Degree Centrality** correctly counts outgoing `:TREATS` or `:HAS_EFFECT` links. |
| **9**  | Commonly used plant parts?          | **Degree Centrality** on `PlantPart` nodes works perfectly.                       |
| **10** | Highest influence on treatments?    | **ArticleRank** handles the recursive influence logic as designed.                |
| **11** | Disease with most phytochemicals?   | **In-degree** count on `Disease` nodes is a standard GDS operation.               |
| **14** | Herb treats the most diseases?      | Simple **Degree** count on `Herb -> Disease` relationships.                       |

---

## 🟡 Partial Success / Biased (Needs Symmetry Fix)
These prompts will return results, but the results may be mathematically "skewed" or "double-counted" due to the missing Symmetry Guardian.

| #      | Prompt                             | The Edge Case / Problem                                                                                                           |
| :----- | :--------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------- |
| **3**  | Groups for similar diseases?       | **Louvain** clustering is sensitive to relationship weight. Duplicate `A <-> B` links will artificially inflate cluster strength. |
| **8**  | Similar chemical structures?       | **Node Similarity** (Jaccard) requires clean neighbor sets. Duplicate links create "noise" in the similarity score.               |
| **12** | Herbs with similar phytochemicals? | Same as above—overlaps are over-counted if the `CONTAINS` link is mirrored inconsistently.                                        |
| **13** | Parts for similar treatments?      | Structural similarity is highly sensitive to graph density.                                                                       |
| **15** | Clusters of diseases?              | Community detection requires a balanced graph to find accurate therapeutic "islands."                                             |

---

## 🔴 High Risk of Failure (Needs Path/RAG Fix)
These prompts require multi-hop reasoning or "backward" traversal which the current directed-only system may miss.

| #     | Prompt                          | Why it might fail                                                                                                                      |
| :---- | :------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------- |
| **2** | Herbs for inflammation?         | If the link is `Herb -> Chemical -> Inflammation`, and the RAG only looks "forward," it won't find the Herb starting from the disease. |
| **4** | Uses for important herbs?       | Requires a "Chain of Rank" (Rank Herb -> Find Links). A single GDS call can't do this; it needs custom Cypher logic.                   |
| **6** | Parts used together?            | Requires co-occurrence analysis (finding nodes that share a common neighbor). Cypher is slow for this at scale.                        |
| **7** | Chemicals connecting two herbs? | This is a **Shortest Path** problem. In a directed graph, if the path is `Herb A -> Chem <- Herb B`, a directed search fails.          |

---

## 🚀 The "End State" Fix
To move all 🔴 and 🟡 cases to 🟢:
1.  **Backend**: Implement the `SYMMETRIC_REL_TYPES` whitelist in `neo4j_utils.py`.
2.  **RAG**: Set `orientation: 'UNDIRECTED'` in the GDS projections used by the chat service.
3.  **UI**: Conditionally hide arrows in `ForceGraph2D` to match the underlying undirected logic.

*Detailed fix instructions are in the [Remediation Plan](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/remediation/remediation_plan.md).*
