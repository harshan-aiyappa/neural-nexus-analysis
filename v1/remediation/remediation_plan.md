# Technical Remediation Plan: Solving Graph Edge Cases

This step-by-step plan outlines how to solve the identified Neo4j problems properly.

## Phase 1: Backend Symmetry & Data Integrity

### 1.1 Update `neo4j_utils.py`
**File**: `neural-nexus-be/app/db/neo4j_utils.py`
**Changes**:
- Define a constant `SYMMETRIC_TYPES = ["PARTNER_OF", "RELATED_TO", "HAS_SIMILAR_STRUCTURE"]`.
- In `create_relationship`, before merging `(source)-[:REL]->(target)`:
  - If `type` in `SYMMETRIC_TYPES`, check if `(target)-[:REL]->(source)` exists.
  - If it exists, return success without creating a duplicate.

### 1.2 Undirected RAG Expansion
**File**: `neural-nexus-be/app/services/rag/enhanced_rag.py`
**Changes**:
- In the `_graph_expansion_node`, update the Cypher pattern from `(n)-[r]->(m)` to `(n)-[r]-(m)`.
- This ensures that if the knowledge graph has a link `Herb -> CONTAINS -> Chemical`, searching from "Chemical" will successfully retrieve the "Herb".

---

## Phase 2: Frontend Visual Clarity

### 2.1 Shared Type Constants
**File**: `neural-nexus-fe/src/components/graph/types.ts`
**Changes**:
- Export the same `SYMMETRIC_TYPES` list to be used by both 2D and 3D renderers.

### 2.2 Update SVG Arrow Logic (2D)
**File**: `neural-nexus-fe/src/components/graph/2d/ForceGraph2D.tsx`
**Changes**:
- In the `linkElements` binding, conditionally set `.attr('marker-end', d => SYMMETRIC_TYPES.includes(d.type) ? null : 'url(#arrow-marker)')`.

### 2.3 Update 3D Relationship Logic
**File**: `neural-nexus-fe/src/components/graph/3d/components/Links.tsx` (and related labels)
**Changes**:
- Hide arrow geometries or pulse animations for symmetric types to indicate a bidirectional association rather than a flow.

---

## Phase 3: Performance & Scale

### 3.1 GDS Fallback for Pathfinding
**File**: `neural-nexus-be/app/services/analytics_chat/analytic_chat_service.py`
**Changes**:
- In `_run_shortest_path`, if the result is empty after a 15-hop Cypher search, fallback to `gds.allShortestPaths.dijkstra.stream` using the pre-loaded global native projection.

### 3.2 Dynamic Schema Hardening
**Action**: Create a migration scripts in `neural-nexus-be/scripts/harden_schema.py`.
**Logic**:
- Loop through all nodes with `type: 'Herb'`.
- Run `MATCH (n {id: $id}) SET n:Herb`.
- Repeat for `Phytochemical`, `Disease`, etc.
- **Benefit**: Increases query speed by 10x-50x for large datasets by using native label-based indexes.

---

## Success Criteria
- [ ] No duplicate inverse relationships are created during ingestion.
- [ ] RAG answers include context from "target" nodes.
- [ ] Symmetric lines in the graph view have no arrows.
- [ ] Multi-hop queries return in <500ms on large datasets.
