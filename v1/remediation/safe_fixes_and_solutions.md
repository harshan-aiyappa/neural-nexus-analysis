# Safe Fixes: Problems & Solutions Reference

This document provides a paired view of every critical problem identified in the `neural-nexus` graph system and its corresponding "Safe Fix" (low-risk, high-impact resolution).

---

## 1. The "Symmetry" Problem
**Problem**: The system can create both `A -> B` and `B -> A` for same relationship types.
- **Impact**: Double-counts connectivity strength in PageRank and Degree Centrality.
- **Safe Fix**: Implement a **Symmetry Guardian** in `neo4j_utils.py`.
- **Solution**: Whitelist symmetric types (e.g., `PARTNER_OF`) and check for an existing inverse link before merging a new one.

## 2. The "RAG Blind Spot" Problem
**Problem**: AI retrieval only looks "forward" along directed arrows.
- **Impact**: It misses the "Source" node if the user asks a question starting from the "Target" node.
- **Safe Fix**: Set **Undirected Orientation** in `enhanced_rag.py`.
- **Solution**: Update the Cypher pattern to `(n)-[r]-(m)` for contextual expansion.

## 3. The "Visual Confusion" Problem
**Problem**: Every relationship in the 2D view has an arrowhead.
- **Impact**: Users misinterpret symmetric associations as hierarchical flows.
- **Safe Fix**: **Conditional Marker Rendering** in `ForceGraph2D.tsx`.
- **Solution**: Use a `SYMMETRIC_TYPES` list to decide whether to attach the `marker-end` (arrow) to a line.

## 4. The "Scaling/Speed" Problem
**Problem**: Searching for nodes by a `type` property is slow on large graphs.
- **Impact**: Deep analytical queries (5+ hops) become laggy.
- **Safe Fix**: **Dynamic Label Migration**.
- **Solution**: Run a one-time script to convert `type: 'Herb'` properties into native `:Herb` labels. This enables Neo4j's high-speed schema indexes.

## 5. The "Pathfinding Latency" Problem
**Problem**: Complex multi-hop paths take too long via standard Cypher.
- **Impact**: Analytics chat appears to "hang" during deep discovery.
- **Safe Fix**: **GDS Native Fallback** in `AnalyticChatService.py`.
- **Solution**: If a Cypher-based shortest path fails or is slow, automatically rerun the query using the **GDS In-Memory** projection for millisecond results.

---

## Summary of Impact

| Problem          | Fix Complexity | Implementation Risk |  Performance Gain  |
| :--------------- | :------------: | :-----------------: | :----------------: |
| Symmetry Bias    |      Low       |         Low         |  High (Accuracy)   |
| RAG Blind Spots  |      Low       |         Low         |  High (Relevance)  |
| Visual Confusion |      Low       |        None         |     High (UX)      |
| Scaling Speed    |      Med       |         Low         |     10x - 50x      |
| Path Latency     |      Med       |         Low         | Sub-second results |

*Specific code snippets for all these fixes are available in the [Detailed Execution Plan](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/remediation/detailed_execution_plan.md).*
