# Find Transfer Bounds

**Purpose**  
Determine the start and end indices of sit-to-stand and stand-to-sit transitions based on vertical head position.

---

## Inputs and Outputs

**Inputs**
| Variable         | Description                        | Source |
|-----------------|-----------------------------------|--------|
| `z`              | Vertical head position over time   | Head motion capture system or AR system |
| `standing_segments` | Indices of standing periods       | DetectTransfers |
| `threshold_speed` | Velocity threshold to detect onset/offset | User-defined |

**Output**
| Variable         | Description                                       |
|-----------------|---------------------------------------------------|
| `sit_to_stand`   | Nx2 array of `[startIdx, endIdx]` for sit-to-stand transitions |
| `stand_to_sit`   | Nx2 array of `[startIdx, endIdx]` for stand-to-sit transitions |

---

## Implementation Notes

- **Backward search**: For sit-to-stand, find the last index before standing where velocity < threshold.  
- **Forward search**: For sit-to-stand, find the first index after valley where vertical position exceeds valley.  
- **Stand-to-sit detection**: Similar logic in reverse, looking for downward velocity peaks and valleys.  
- **Validation**: Only store transitions where both start and end indices exist.  

---

## Pseudocode

```text
// Step 1: Compute vertical speed
speed = gradient(z)

// Step 2: Loop over each standing segment
for each segment in standing_segments:

    // Sit-to-stand backward search for valley
    j = segment start
    while j>1 AND speed[j] >= threshold_speed: j=j-1
    end_valley = j

    // Sit-to-stand forward search for start
    while j < length(z) AND z[j] <= z[end_valley]: j=j+1
    start_stand = j if j>1 else NaN

    if start_stand not NaN:
        j = segment start
        while j < segment end AND speed[j] >= threshold_speed: j=j+1
        start_peak = j
        while j>1 AND z[j] >= z[start_peak]: j=j-1
        end_stand = j
    else:
        end_stand = NaN

    // Stand-to-sit backward search for peak
    j = segment end
    while j>1 AND speed[j] <= -threshold_speed: j=j-1
    end_peak = j

    // Stand-to-sit forward search for start
    while j < length(z) AND z[j] >= z[end_peak]: j=j+1
    start_sit = j if j < length(z) else NaN

    if start_sit not NaN:
        j = segment end
        while j < length(z) AND speed[j] <= -threshold_speed: j=j+1
        start_valley = j
        while j>1 AND z[j] <= z[start_valley]: j=j-1
        end_sit = j
    else:
        end_sit = NaN

// Step 3: Keep only valid pairs
sit_to_stand = all start_stand/end_stand pairs where both not NaN
stand_to_sit = all start_sit/end_sit pairs where both not NaN
