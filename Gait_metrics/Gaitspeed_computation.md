# Maximum Gait Speed

**Purpose**  
Compute the maximum gait speed from horizontal head position using filtered displacement.

---

## Inputs and Outputs

**Inputs**
| Variable | Description | Source |
|----------|-------------|--------|
| `pos`   | 2D head position (x, y) over time | Motion capture or AR system |
| `Fs`    | Sampling frequency (Hz)           | Motion capture or AR system |

**Output**
| Variable     | Description |
|--------------|-------------|
| `max_speed`  | Maximum gait speed (m/s) |

---

## Parameters (Recommended Defaults)

| Parameter       | Description | Suggested Value |
|-----------------|-------------|----------------|
| `cutoff`        | Low-pass filter cutoff frequency | 4 Hz |
| `filter_order`  | Order of Butterworth filter      | 2 |
| `smoothWin`     | Window for moving average smoothing | 0.5 s (rounded to samples) |
| `trim`          | Frames to remove at start/end to avoid filter transients | 25 |

---

## Implementation Notes

- **Smoothing**: Use a moving average to smooth instantaneous speed.  
- **Trimming**: Remove filter transient effects at start and end before computing max.  

---

## Pseudocode

```text
// Step 1: Design low-pass Butterworth filter
cutoff = 4 Hz
[b, a] = 2nd-order Butterworth low-pass filter with cutoff / (Fs/2)

// Step 2: Filter horizontal positions (x, y)
pos_filt = apply zero-phase filter to pos

// Step 3: Compute frame-to-frame displacement
delta = difference between consecutive rows of pos_filt
step_dist = Euclidean distance of delta

// Step 4: Convert to instantaneous speed
speed = step_dist * Fs

// Step 5: Smooth instantaneous speed
smoothWin = round(Fs/2)   // 0.5-second moving average
speed_smooth = moving average of speed with smoothWin

// Step 6: Remove filter transients
trim = 25 frames
speed_smooth = speed_smooth[trim : end-trim]

// Step 7: Compute maximum speed
max_speed = max(speed_smooth)
