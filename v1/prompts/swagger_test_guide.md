# Swagger Test Guide: 15 Analytical Prompts

Use this guide to test your 15 specific botanical and analytical prompts using the backend Swagger UI at **http://10.10.20.86:8000/docs**.

---

## 🚀 The "All-in-One" Natural Language Tester
Most of your 15 prompts can be tested directly using the **Analytical Chat** endpoint. This endpoint uses an LLM to automatically pick the right GDS algorithm.

**Endpoint**: `POST /analytics/query`
**Payload Example**:
```json
{
  "query": "Which phytochemicals are most important in the network?",
  "folder_id": "your_folder_id_here"
}
```

---

## 🔬 Specific Algorithm Testing
If you want to test the raw algorithms without the LLM, use these specific endpoints:

| Prompt              | Scenario       | Swagger Endpoint                     | Params to Set                      |
| :------------------ | :------------- | :----------------------------------- | :--------------------------------- |
| **1, 10**           | **Influence**  | `GET /analytics/centrality/pagerank` | `top_k=10`                         |
| **4, 5, 9, 11, 14** | **Hubs**       | `GET /analytics/centrality/degree`   | `top_k=10`                         |
| **8, 12, 13**       | **Similarity** | `GET /analytics/similarity/nodes`    | `similarity_cutoff=0.1`            |
| **3, 6, 15**        | **Clustering** | `GET /analytics/community/louvain`   |                                    |
| **2, 7**            | **Multi-hop**  | `POST /reasoning/consult`            | `message="Herbs for inflammation"` |

---

## 🛠️ Testing Instructions (Step-by-Step)

1.  **Authorize**: Click the "Authorize" button at the top of Swagger and paste your JWT token (if authentication is enabled).
2.  **Pick an Endpoint**: Open one of the routes listed in the table above.
3.  **Try it Out**: Click "Try it out".
4.  **Enter Parameters**: 
    - Always provide a `folder_id` if your data is scoped to a folder.
    - For `node_ids`, you can leave it empty to analyze the whole folder.
5.  **Execute**: Click the big blue "Execute" button.
6.  **Verify**:
    - **Code 200**: Success! Check the `results` and `insight` fields in the response body.
    - **Code 500/400**: Failure. Check if the graph projection exists in the backend logs.

---

## ⚠️ Known Status during Testing
- **✅ Works**: All Centrality (PageRank/Degree) and Similarity (Jaccard) prompts.
- **⚠️ Needs Work**: Multi-hop pathfinding (Prompt 2) may return incomplete results until the **Symmetry Guardian** and **Undirected RAG** fixes from the [Remediation Plan](file:///d:/01_Projects/OpenSource/neural-nexus/analysis/remediation/remediation_plan.md) are applied.
