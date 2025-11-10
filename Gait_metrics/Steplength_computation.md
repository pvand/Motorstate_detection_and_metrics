# Step Length Computation

**Purpose**  
Compute step lengths based on horizontal head position and detected gait events (heel strikes or double support phases).

---

## Inputs and Outputs

**Inputs**
| Variable       | Description                                  | Source |
|----------------|----------------------------------------------|--------|
| `pos`          | 2D head position (x, y) over time           | Motion capture or AR system |
| `event_idx`    | Indices of heel strikes (Theia3D) or double support phases (AR) | Detected gait events |
| `Fs`           | Sampling frequency (Hz)                      | Motion capture or AR system |

**Output**
| Variable       | Description |
|----------------|-------------|
| `step_lengths` | Step lengths for each consecutive step event |

---

## Parameters (Recommended Defaults)

| Parameter       | Description | Suggested Value |
|-----------------|-------------|----------------|
| `cutoff`        | Low-pass filter cutoff frequency | 4 Hz |
| `filter_order`  | Order of Butterworth filter      | 2 |

---

## Implementation Notes

- **Filtering**: Apply a low-pass Butterworth filter to 2D horizontal positions to remove high-frequency noise.  
- **Event extraction**: Use positions at detected gait events (heel strikes or double support).  
- **Step length**: Compute Euclidean distance between consecutive event positions.  

---

## Pseudocode

```text
// Step 1: Design low-pass Butterworth filter
cutoff = 4 Hz
[b, a] = butter(order=2, cutoff / (Fs/2))

// Step 2: Filter horizontal 2D positions
pos_filtered = apply filtfilt(b, a, pos[:,1:2])

// Step 3: Extract positions at detected gait events
event_pos = pos_filtered[event_idx, :]

// Step 4: Compute step lengths
step_lengths = empty list
for each consecutive pair of points in event_pos:
    step_length = Euclidean distance between points
    append step_length to step_lengths
