# Detect Turning Events

**Purpose**  
Detect turning events based on head yaw angle using smoothed angular velocity.

---

## Inputs and Outputs

**Inputs**
| Variable       | Description                                  | Source |
|----------------|----------------------------------------------|--------|
| `yaw`          | Yaw angle signal over time                   | Head Theia3D or AR system |
| `Fs`           | Sampling frequency (Hz)                      | Head Theia3D or AR system |
| `velThreshold` | Minimum angular velocity to count as a turn  | User-defined |

**Output**
| Variable   | Description                        |
|------------|------------------------------------|
| `turn_idx` | Indices of detected turning events |

---

## Pseudocode

```text
Input: yaw angle signal (yaw)
Output: Turning indices (turn_idx)

// Step 1: Compute smoothed yaw velocity
ang_Vel = gradient(yaw) * Fs                     // compute angular velocity
velFilt = smooth ang_Vel using Epanechnikov kernel of specified duration (Shah et al., 2021)
velAbs = abs(velFilt)                             // take absolute value

// Step 2: Detect candidate valleys
locs = find valleys in -velAbs with minimum peak prominence

// Step 3: Identify valid turn peaks
for each valley index ii from 1 to length(locs)-1:  
    segment = velAbs[locs[ii] : locs[ii+1]]      // extract segment between consecutive valleys  
    peakVal, peakRelIdx = max(segment)           // find maximum in segment  
    if peakVal > velThreshold:                   // check against threshold  (Ve = 50 °/s  )
        append segment to turn_idx

---

## References

Shah, V. V., Curtze, C., Mancini, M., Carlson-Kuhta, P., Nutt, J. G., Gomez, C. M., El-Gohary, M., Horak, F. B., & McNames, J. (2021). Inertial Sensor Algorithms to Characterize Turning in Neurological Patients With Turn Hesitations. IEEE transactions on bio-medical engineering, 68(9), 2615–2625. https://doi.org/10.1109/TBME.2020.3037820




