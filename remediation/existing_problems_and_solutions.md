# Existing Problems & Solutions

Based on the graph architecture analysis, here are the identified problems (edge cases) and their corresponding technical solutions.

## 1. Visual Over-Directionality
**Problem**: The 2D and 3D graph visualizations (`ForceGraph2D`, `NeuralSpace3D`) render all relationships with arrows. Symmetric relationships (e.g., "Symmetric Property", "Partner") appear hierarchical, which leads to user confusion.
**Solution**:
- **Implementation**: Create a `SYMMETRIC_RELATIONSHIPS` list in `types.ts`.
- **UI Update**: Modify the SVG/Three.js renderers to hide the `marker-end` (arrow) if the relationship type is in that list.

## 2. Retrieval Retrieval (RAG) Blind Spots
**Problem**: The `enhanced_rag.py` service expansion logic often uses directed Cypher patterns (`(n)-[:REL]->(m)`). If a user queries the "target" node, the "source" node might be missed.
**Solution**:
- **Implementation**: Update `_graph_expansion_node` in `enhanced_rag.py` to use undirected relationship matches (`-[:REL]-`) for types known to be non-hierarchical.
- **Benefit**: Ensures context is gathered from both directions, improving the "grounding score" and factual accuracy of AI answers.

## 3. Duplicate Symmetric Relationships
**Problem**: Without the Java Traversal Framework's symmetry handling, the system can accidentally create both `A -> REL -> B` and `B -> REL -> A`. This biases graph algorithms (e.g., double-counting PageRank scores).
**Solution**:
- **Implementation**: Update `create_relationship` in `neo4j_utils.py` to include a symmetry check.
- **Logic**: For specific types, check if `(target)-[:REL]->(source)` already exists before creating `(source)-[:REL]->(target)`.
- **Validation**: Run a one-time cleanup script using APOC to merge duplicate inverse relationships.

## 4. Query Performance on Deep Paths
**Problem**: Complex, multi-hop traversals (e.g., finding connections 5+ hops away) can be slow in pure Cypher for large datasets.
**Solution**:
- **Implementation**: Use **GDS Native Projections** for complex queries.
- **Logic**: Instead of Cypher pathfinding, use `gds.allShortestPaths.dijkstra` or `gds.bfs.stream` on a pre-loaded graph to achieve sub-millisecond responses on large graphs.

## 5. Metadata/Label Fragility
**Problem**: The system relies on a single `Entity` label + a `type` property. Cypher queries can be fragile if the `type` property is missing or misspelled.
**Solution**:
- **Implementation**: Use Neo4j **Dynamic Labeling**.
- **Logic**: During ingestion, apply both `:Entity` and a specific label (e.g., `:Phytochemical`). This allows Neo4j to use its schema indexes more efficiently, making queries faster and more robust.
