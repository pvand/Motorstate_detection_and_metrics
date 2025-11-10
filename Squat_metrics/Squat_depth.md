# Squat Depth

**Purpose**  
Compute the vertical displacement (depth) during a squat segment relative to a baseline.

---

## Inputs and Outputs

**Inputs**
| Variable       | Description                                         | Source |
|----------------|-----------------------------------------------------|--------|
| `segments`     | List of turning segments with start and end indices | (Squat_bounds) |
| `VT_smooth`    | Smoothed vertical position signal over time        | AR or Theia3D system |
  
**Output**
| Variable       | Description                                     |
|----------------|-------------------------------------------------|
| `depth`        | Absolute vertical displacement during the turn segment |

---

## Pseudocode

```text
// Step 1: Loop over all turn segments
for each segment in segments:

    // Extract indices for current segment
    seg_idx = segment.start : segment.end

    // Step 2: Compute baseline vertical position
    baseline = (VT_smooth[segment.start] + VT_smooth[segment.end]) / 2

    // Step 3: Compute minimum vertical position within segment
    min_VT = min(VT_smooth[seg_idx])

    // Step 4: Compute depth
    segment.depth = abs(baseline - min_VT)
