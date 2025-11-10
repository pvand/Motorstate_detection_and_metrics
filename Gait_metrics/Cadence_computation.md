# Cadence

**Purpose**  
Compute walking cadence (steps per minute) from detected gait events, either heel strikes (Theia3D) or double support phases (AR).

---

## Inputs and Outputs

**Inputs**
| Variable      | Description                                     | Source |
|---------------|-------------------------------------------------|--------|
| `event_idx`   | Indices of heel strikes (Theia3D) or double support phases (AR) | Detected gait events |
| `Fs`          | Sampling frequency (Hz)                        | Motion capture or AR system |

**Output**
| Variable    | Description |
|------------|-------------|
| `cadence`  | Walking cadence in steps per minute |

---

## Implementation Notes

- **Event consistency**: Ensure `event_idx` is correctly ordered in time.  
- **Step interval**: Use median interval between consecutive events to avoid influence of outliers.  

---

## Pseudocode

```text
// Step 1: Compute median frame difference between consecutive steps
median_diff = median(diff(event_idx[1:end]))

// Step 2: Convert median step interval to cadence (steps per minute)
cadence = (60 * Fs) / median_diff
