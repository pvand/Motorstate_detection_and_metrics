# Detect Straight Walking Segments

**Purpose**  
This algorithm identifies straight walking segments based on 2D head position and head yaw orientation.  
It ensures that detected segments are long enough, straight enough, and performed with sufficient forward motion.

---
## Inputs and Outputs

**Inputs**
| Variable | Description | Source |
|-----------|--------------|-------|
| `x, y` | 2D head position | Head motion capture system or AR system |
| `yaw` | Head yaw angle | Head motion capture system or AR system |
| `Fs` | Sampling frequency | Head motion capture system or AR system |

**Output**
| Variable | Description |
|-----------|--------------|
| `walk\_idx` | Nx2 array of `\[start\_index, end\_index]` marking each straight-walking segment |

---

## Parameters (Recommended Defaults)

| Parameter | Description | Suggested Value |
|------------|--------------|-----------------|
| `min\_dist` | Minimum walking distance for a valid segment | 2.0 m |
| `max\_heading\_range` | Maximum allowed heading deviation | 45° |
| `max\_yaw\_range` | Maximum allowed yaw deviation | 45° |
| `min\_speed` | Minimum speed for valid walking | 0.05 m/s |
| `uniform\_spacing` | Interpolation resolution for arc-length | 0.01 m |

---

## Implementation Notes

- \*\*Coordinate smoothing\*\*: Apply a low-pass filter (e.g., 4 Hz Butterworth) before computing speed to suppress sensor noise.
- \*\*Angle wrapping\*\*: Use `unwrap()` to handle 0–360° (or ±π) discontinuities before computing heading differences.
- \*\*Units\*\*: Ensure heading and yaw are in \*\*consistent angular units\*\*.
- \*\*Minimum segment length\*\*: Adjust `min\_dist` based on use (e.g., >=2 m recommended for step detection).
- \*\*Interpolation\*\*: Uniform interpolation along path length prevents bias from irregular sampling or stop–go motion.

---

## Pseudocode

```text
// Step 1: Compute cumulative distance and instantaneous speed
ds = distance between consecutive (x, y)
arc\_length = cumulative sum of ds
speed = ds \* Fs

// Step 2: Compute smoothed heading direction
interpolate x and y to uniform spacing (0.01 m) along arc\_length
dx, dy = gradient of interpolated x and y
heading\_angle = unwrap(atan2(dy, dx))
heading\_angle\_interp = interpolate heading\_angle back to original points

// Step 3: Identify straight-walking points
initialize valid\_points = false for all samples
for each start index i:
  for each end index j > i where (arc\_length\[j] - arc\_length\[i]) ≥ min\_dist:
    segment\_heading = heading\_angle\_interp\[i:j]
    segment\_yaw = yaw\[i:j]
    segment\_speed = speed\[i:j]
    if (range(segment\_heading) ≤ max\_heading\_range) AND
       (range(segment\_yaw) ≤ max\_yaw\_range) AND
       (all(segment\_speed ≥ min\_speed)):
       mark valid\_points\[i:j] = true
    else:
    break // stop extending segment when thresholds exceeded

// Step 4: Extract contiguous segments
find start and end indices of consecutive true values in valid\_points
for each segment \[s, e]:
   if (arc\_length\[e] - arc\_length\[s] ≥ min\_dist) AND (mean(speed\[s:e]) ≥ min\_speed):
     append \[s, e] to walk\_idx




