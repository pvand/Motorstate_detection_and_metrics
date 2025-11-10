# Detect Standing Segments

**Purpose**  
Identify standing periods from vertical head position using clustering.

---

## Inputs and Outputs

**Inputs**
| Variable | Description | Source |
|----------|-------------|--------|
| `pos`   | Smoothed vertical head position (z) over time | Head motion capture system or AR system |
| `k`     | Number of clusters for k-means | Algorithm parameter (default = 2) |

**Output**
| Variable       | Description                       |
|----------------|-----------------------------------|
| `standing_idx` | Indices corresponding to standing periods |

---

## Parameters (Recommended Defaults)

| Parameter | Description                          | Suggested Value |
|-----------|--------------------------------------|----------------|
| `k`       | Number of clusters for k-means       | 2 |

---

## Implementation Notes

- **Clustering**: K-means clustering separates the signal into `k` states; typically `k=2` to distinguish standing from non-standing.  

---

## Pseudocode

```text
// Step 1: Cluster vertical position into k states
k = 2
cluster_idx, centroids = k-means clustering of pos into k clusters

// Step 2: Identify standing cluster
standing_cluster = cluster with highest centroid value

// Step 3: Extract indices belonging to standing cluster
standing_idx = indices where cluster_idx == standing_cluster
