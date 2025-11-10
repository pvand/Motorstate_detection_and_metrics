# Algorithm 4 — Detect Standing Segments

**Purpose**  
Identify standing periods from vertical head position using clustering.

---

## Inputs and Outputs

**Inputs**
| Variable | Description | Source |
|----------|-------------|--------|
| `pos`   | Smoothed vertical head position (z) over time | Head Theia3D or AR system |
| `k`     | Number of clusters for k-means (default = 2) | Algorithm parameter |

**Output**
| Variable       | Description                       |
|----------------|-----------------------------------|
| `standing_idx` | Indices corresponding to standing periods |

---

## Pseudocode

```text
Input: vertical head position (z)
Output: Standing segments (standing_idx)

// Step 1: Cluster vertical position into two states
k = 2
cluster_idx, centroids = k-means clustering of pos into k clusters

// Step 2: Identify standing cluster
standing_cluster = cluster with highest centroid value

// Step 3: Extract indices belonging to standing cluster
standing_idx = indices where cluster_idx == standing_cluster
