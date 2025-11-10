# Find Squat Bounds

**Purpose**  
Determine the start and end indices of squat segments based on vertical head position and previously detected squat valleys.

---

## Inputs and Outputs

**Inputs**
| Variable    | Description                        | Source |
|-------------|-----------------------------------|--------|
| `z`         | Vertical head position over time   | Head motion capture system or AR system |
| `squat_idx` | Indices of detected squat valleys  | DetectSquats) |

**Output**
| Variable         | Description                                   |
|-----------------|-----------------------------------------------|
| `squat_segments` | Nx2 array of `[startIdx, endIdx]` marking each squat segment |

---

## Implementation Notes

- **Backward search**: For each squat valley, find the last index before the valley where velocity > 0 (movement onset).  
- **Forward search**: For each squat valley, find the first index after the valley where velocity < 0 (movement offset).  
- **Segment validation**: Store only segments where both start and end indices exist.  

---

## Pseudocode

```text
// Step 1: Compute vertical speed
speed = gradient(z)

// Step 2: Initialize outputs
start_idx = empty list
end_idx   = empty list

// Step 3: For each detected squat valley
for each valley in squat_idx:

    // Search backward for movement onset
    start = last index before valley where speed > 0

    // Search forward for movement offset
    end = first index after valley where speed < 0

    // Store only if both start and end exist
    if start and end exist:
        append [start, end] to squat_segments
