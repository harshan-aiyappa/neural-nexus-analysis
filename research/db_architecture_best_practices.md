# Database Architecture: Single vs. Multi-DB Best Practices

This document compares the "Single DB with Logical Folders" approach (current) against the "Multi-Database" approach in Neo4j, evaluating them against global industry standards.

---

## 1. Single Database with Logical Partitioning (Current)
*Global Standard: "Attribute-Based Access Control (ABAC)"*

In this model, all data lives in one physical database, and a property (like `folder_id`) is used to separate data.

### ✅ Why it's a Good Practice:
- **Resource Efficiency**: Low overhead. You don't need to spin up new database instances for every project.
- **Cross-Folder Insights**: If a researcher wants to compare "Herbology" with "Pharmacology" folders, the system can do it easily because the data is in the same graph.
- **Simplified Maintenance**: One backup, one set of indexes, and one connection string to manage.

### ⚠️ The Weakness:
- **Security**: If a developer forgets to add `WHERE folder_id = 'X'`, data from other folders might "leak" into the response.
- **Performance**: As the graph grows to millions of nodes, property-based filtering can slow down unless **Native Labels** (e.g., `:Folder_X`) are used.

---

## 2. Multi-Database / Multi-Tenancy (Neo4j 4.0+)
*Global Standard: "Physical Isolation"*

In this model, every "Folder" would be a completely separate database within the Neo4j instance.

### ✅ Why it's a Best Practice:
- **Hard Isolation**: It is physically impossible for data from one database to appear in another. Highest security.
- **Custom Tuning**: You can give more RAM/CPU to a "Heavy Research" database while limiting a "Personal" one.
- **Per-Tenant Backups**: You can backup or restore a single researcher's work without touching others.

### ⚠️ The Weakness:
- **Complex Orchestration**: The backend must constantly switch connection strings or use `USE databaseName` in Cypher.
- **Zero Cross-Talk**: You cannot run a GDS algorithm across two databases. They are completely isolated "islands."
- **Idle Overhead**: Many small databases can consume more memory than one large one.

---

## 3. Global Standard Recommendation: The "Hybrid" Approach

For a product like `neural-nexus` (collaborative botanical research), the **Single DB with Native Labels** is the global best practice.

### Why the Hybrid Approach?
Instead of just a `folder_id` property, we recommend **Dynamic Labeling**:
- Give nodes a folder-specific label: `:Folder_123`.
- **Why?**: Neo4j indexes labels natively. A search for `MATCH (n:Folder_123)` is as fast as a search in a separate database, but you still keep the ability to do cross-folder analytics if needed.

| Feature        | Single DB (Logical) | Multi-DB (Physical) |     **Recommendation**     |
| :------------- | :-----------------: | :-----------------: | :------------------------: |
| Isolation      |        Soft         |        Hard         |     **Logical (Soft)**     |
| Performance    |       Indexed       |       Native        | **Hybrid (Native Labels)** |
| Cross-Analysis |        Easy         |     Impossible      |       **Single DB**        |
| Resource Cost  |         Low         |        High         |       **Single DB**        |

### Summary
Stick with the **Single Database** architecture to keep the product flexible and fast. However, implement the **Native Labeling** fix from our [Remediation Plan](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/remediation/detailed_execution_plan.md) to ensure physical-speed isolation at a global scale.
