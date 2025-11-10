# Detect Squat Segments

**Purpose**  
Identify squat events from vertical head position using clustering and optional pitch correction for AR systems.

---

## Inputs and Outputs

**Inputs**
| Variable | Description | Source |
|----------|-------------|--------|
| `z`      | Vertical head position over time | Head motion capture or AR system |
| `pitch`  | Head pitch angle over time (radians) | AR system |
| `L`      | Scaling factor for pitch correction (meters) | User-defined |
| `k`      | Number of clusters for k-means | Algorithm parameter (default = 2) |

**Output**
| Variable   | Description |
|------------|-------------|
| `squat_idx` | Indices of detected squat events |

---

## Parameters (Recommended Defaults)

| Parameter | Description                            | Suggested Value |
|-----------|----------------------------------------|----------------|
| `k`       | Number of clusters for k-means         | 2 |
| `L`       | Scaling factor for pitch correction    | 0.1 – 0.3 m (depending on coordinate system origin headset) |

---

## Implementation Notes

- **Pitch correction**: For AR system adjust vertical head position to remove forward/backward head tilt before detecting minima.  
- **Clustering**: Separate shallow and deep valleys using k-means; deeper valleys are interpreted as valid squats.  

---

## Pseudocode

```text
// Step 1: Correct for pitch (AR headset only)
if AR system
    correction = L * (1 - cos(pitch))       // pitch-based vertical correction
    z = z + correction                 // corrected vertical position

// Step 2: Detect valleys (squat minima)
valley_idx = find local minima of z_corr
valley_heights = z_corr[valley_idx]

// Step 3: Cluster valleys into deep vs shallow
cluster_idx = k-means clustering of valley_heights into 2 clusters
group1 = valleys where cluster_idx == 1
group2 = valleys where cluster_idx == 2

// Step 4: Select deeper valleys as valid squat events
if mean(z_corr[group1]) < mean(z_corr[group2]):
    squat_idx = group1
else:
    squat_idx = group2

