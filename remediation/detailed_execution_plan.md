# Detailed Technical Execution Plan

This document provides high-resolution instructions for implementing the identified fixes. This plan covers data integrity, RAG reasoning, and UI visual clarity.

---

## 1. Backend: Symmetry & Integrity (Data Protection)

### 1.1 Implementation of "Symmetry Guardian"
**Goal**: Prevent duplicate inverse relationships for symmetric concepts.
- **File**: `neural-nexus-be/app/db/neo4j_utils.py`
- **Logic**:
    ```python
    SYMMETRIC_REL_TYPES = {"PARTNER_OF", "RELATED_TO", "CO_OCCURS_WITH"}

    async def create_relationship_safe(tx, node1_id, node2_id, rel_type):
        if rel_type in SYMMETRIC_REL_TYPES:
            # Check for existing inverse
            check_query = "MATCH (a {id: $id2})-[r]->(b {id: $id1}) WHERE type(r) = $type RETURN r"
            res = await tx.run(check_query, id1=node1_id, id2=node2_id, type=rel_type)
            if await res.single():
                return  # Skip creating duplicate
        
        # Original Merge Logic
        await tx.run("MATCH (a {id: $id1}), (b {id: $id2}) MERGE (a)-[r:REL {type: $type}]->(b)", ...)
    ```

### 1.2 Undirected RAG Context Expansion
**Goal**: Ensure RAG fetches context regardless of relationship direction.
- **File**: `neural-nexus-be/app/services/rag/enhanced_rag.py`
- **Logic**:
    - Locate the `_expand_graph` method.
    - Change any `(n)-[r]->(m)` patterns to `(n)-[r]-(m)`.
    - **Crucial**: Filter by relationship type to avoid "over-retrieval" from super-hubs.
    ```cypher
    MATCH (n {id: $id})-[r]-(m) 
    WHERE type(r) IN $contextual_types 
    RETURN m.content, labels(m)[0] as type
    ```

---

## 2. Global Analytics: Performance & Scaling

### 2.1 Dynamic Label Hardening
**Goal**: Convert property-based lookups to native label lookups for 10x-50x speed gains.
- **Action**: Create a migration script `scripts/migrate_labels.py`.
- **Snippet**:
    ```cypher
    MATCH (n:Entity) 
    WHERE n.type = 'Phytochemical' 
    SET n:Phytochemical;
    
    MATCH (n:Entity) 
    WHERE n.type = 'Herb' 
    SET n:Herb;
    ```
- **Update**: Modify `AnalyticChatService` to use `:Herb` directly in Cypher instead of `WHERE n.type = 'Herb'`.

---

## 3. Frontend: Logic-Driven UI Clarity

### 3.1 Conditional Arrow Rendering (D3.js)
**Goal**: Remove arrows from symmetric relationships in the 2D view.
- **File**: `neural-nexus-fe/src/components/graph/2d/ForceGraph2D.tsx`
- **Implementation**:
    - Define `const SYMMETRIC_TYPES = ['PARTNER_OF', ...]` inside the component.
    - Inside `updateLinks()` function:
    ```javascript
    linkElements.attr('marker-end', d => 
        SYMMETRIC_TYPES.includes(d.type) ? null : 'url(#arrow-marker)'
    );
    ```

### 3.2 3D Visual Cues
**Goal**: Use line styles (dashed vs solid) to differentiate relationship types in 3D.
- **File**: `neural-nexus-fe/src/components/graph/3d/NeuralSpace3D.tsx`
- **Logic**: For `SYMMETRIC_TYPES`, use a simple line without the directional animated pulse.

---

## 4. Analytical Prompts: Edge Case Coverage
To ensure all 15 prompts work globally:
1. **Fallback Logic**: If `shortestPath` (Cypher) fails due to depth or complexity, `AnalyticChatService` should automatically trigger `gds.allShortestPaths.dijkstra.stream` using the global undirected projection.
2. **Community Stability**: Run `gds.louvain` with a fixed `seedProperty` to ensure that "Groups of Phytochemicals" (Prompt 3) don't shift randomly between analytical requests.

---

## Verification Checklist
- [ ] No duplicate `A <-> B` links after safe-merge implementation.
- [ ] AI correctly identifies "Source" node when starting from "Target" node in RAG expansion.
- [ ] Graph view shows arrows ONLY on directed relationships.
- [ ] Cluster queries on 10,000+ nodes complete in under 1 second.
