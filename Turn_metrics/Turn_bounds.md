# Find Turn Bounds

**Purpose**  
Determine the start and end indices of turning segments based on yaw angle and previously detected turn indices.

---

## Inputs and Outputs

**Inputs**
| Variable   | Description                                      | Source |
|------------|-------------------------------------------------|--------|
| `yaw`      | Yaw angle signal over time                       | Theia3D pelvis or AR head motion |
| `turn_idx` | Indices of detected turning events               | DetectTurns |
| `Vx`       | Backward threshold to detect start of turn      | User-defined |
| `Vc`       | Forward threshold to detect end of turn         | User-defined |

**Output**
| Variable        | Description                       |
|-----------------|-----------------------------------|
| `turn_segments` | Nx2 array of `[startIdx, endIdx]` marking each turn segment |

---

## Implementation Notes

- **Backward search**: Traverse from each turn peak backward until yaw falls below `Vx`.  
- **Forward search**: Traverse from each turn peak forward until yaw falls below `Vc`.  
- **Segment storage**: Each segment is stored as `[startIdx, endIdx]`.  
- **Threshold tuning**: Adjust `Vx` and `Vc` depending on expected turn magnitude and noise.  

---

## Pseudocode

```text
// Step 1: Initialize list of segments
turn_segments = empty list

// Step 2: Loop over each detected turn index
for each peakIdx in turn_idx:

    // Step 2a: Backward search for start of turn
    startIdx = peakIdx
    while startIdx > 1 AND yaw[startIdx] > Vx:
        startIdx = startIdx - 1

    // Step 2b: Forward search for end of turn
    endIdx = peakIdx
    while endIdx < length(yaw) AND yaw[endIdx] > Vc:
        endIdx = endIdx + 1

    // Step 2c: Store segment
    append [startIdx, endIdx] to turn_segments
