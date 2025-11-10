# Turn Peak Velocity

**Purpose**  
Compute the peak angular velocity during turning segments.

---

## Inputs and Outputs

**Inputs**
| Variable            | Description                                         | Source |
|--------------------|-----------------------------------------------------|--------|
| `segments`          | List of turning segments with start and end indices | (Turn_bounds) |
| `yaw`               | Yaw angle signal over time                           | head motion capture system or AR system |
| `duration`          | Duration parameter for Shah filter                  | User-defined |
| `Fs`                | Sampling frequency (Hz)                              | System specification |

**Output**
| Variable           | Description                                     |
|-------------------|-------------------------------------------------|
| `peak_vel`         | Maximum absolute angular velocity during each turn segment |

---

## Pseudocode

```text
// Step 1: Loop over all turn segments
for each segment in segments:

    // Extract indices for current segment
    idx = segment.start : segment.end

    // Step 2: Compute filtered angular velocity
    yaw_segment = yaw[idx]
    vel_filt = shah_filter(yaw_segment, duration, Fs)  // smooth angular velocity
    vel_abs = abs(vel_filt)                             // absolute value

    // Step 3: Store peak velocity
    segment.peak_vel = max(vel_abs)
