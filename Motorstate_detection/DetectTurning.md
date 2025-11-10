#  Detect Turning Events

**Purpose**  
Detect turning events based on head yaw angle using smoothed angular velocity.

---

## Inputs and Outputs

**Inputs**
| Variable       | Description                                  | Source |
|----------------|----------------------------------------------|--------|
| `yaw`          | Yaw angle signal over time                   | Head motion capture system or AR system |
| `Fs`           | Sampling frequency (Hz)                      | Head motion capture system or AR system |
| `velThreshold` | Minimum angular velocity to count as a turn  | User-defined e.g. 50°/s|

**Output**
| Variable   | Description                        |
|------------|------------------------------------|
| `turn_idx` | Indices of detected turning events |

---

## Parameters (Recommended Defaults)

| Parameter       | Description                          | Suggested Value |
|-----------------|--------------------------------------|----------------|
| `min_peak_prominence` | Minimum prominence to detect valleys | User-defined|

---

## Implementation Notes

- **Smoothing**: use algorithm adapted from Shah et al. (2021), doi: 10.1109/TBME.2020.3037820 
- **Valley detection**: Detect local minima in the negative of the absolute smoothed velocity.  
- **Turn validation**: Check that peak velocity exceeds `velThreshold` before registering a turn.  

---

## Pseudocode

```text
// Step 1: Compute smoothed yaw velocity
ang_Vel = gradient(yaw) * Fs                     // compute angular velocity
velFilt = smooth ang_Vel using Epanechnikov kernel of specified duration
velAbs = abs(velFilt)                             // take absolute value

// Step 2: Detect candidate valleys
locs = find valleys in -velAbs with minimum peak prominence

// Step 3: Identify valid turn peaks
initialize turn_idx = []
for each valley index ii from 1 to length(locs)-1:  
    segment = velAbs[locs[ii] : locs[ii+1]]      // extract segment between consecutive valleys  
    peakVal, peakRelIdx = max(segment)           // find maximum in segment  
    if peakVal > velThreshold:                   // check against threshold
        append segment to turn_idx




