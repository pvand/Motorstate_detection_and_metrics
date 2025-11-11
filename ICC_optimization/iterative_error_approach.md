# Find Optimal ICC for Turn Detection Parameters

**Purpose**  
Identify the best combination of head and pelvis angular velocity thresholds (`vx` and `vc`) for AR and Theia systems that maximizes the ICC of turn durations across trials.

---
## Inputs and Outputs

**Inputs**

| Variable         | Description | Source |
|-----------------|-------------|--------|
| `final\_struct`  | Trial data containing AR and Theia yaw signals | Experiment data |
| `vx\_pelvis\_vals` | Range of pelvis velocity thresholds to test | User-defined |
| `vc\_pelvis\_vals` | Range of pelvis correction thresholds to test | User-defined |
| `vx\_head\_vals`  | Range of head velocity thresholds to test | User-defined |
| `vc\_head\_vals`  | Range of head correction thresholds to test | User-defined |


**Outputs**

| Variable      | Description |
|---------------|-------------|
| `best\_ICC`    | Highest ICC achieved across all parameter combinations |
| `best\_params` | `\[vx\_pelvis, vc\_pelvis, vx\_head, vc\_head]` combination that produced `best\_ICC` |

---

## Parameters (Recommended Defaults)

| Parameter | Description | Suggested Value |
|-----------|-------------|----------------|
| `vx\_pelvis\_vals` | Candidate pelvis velocity thresholds | 15–25 deg/s |
| `vc\_pelvis\_vals` | Candidate pelvis correction thresholds | 35–45 deg/s |
| `vx\_head\_vals` | Candidate head velocity thresholds | 20–30 deg/s |
| `vc\_head\_vals` | Candidate head correction thresholds | 10–20 deg/s |

---

## Implementation Notes
- **Segment detection**: Use velocity thresholds to detect valid turns in pelvis and head signals.  
- **Threshold refinement**: Use `Turn\_bounds` to adjust segment start and end indices based on velocity parameters.  
- **Segment matching**: Align AR and Theia turn segments using `matchSegments`.  
- **ICC computation**: Calculate ICC for mean turn durations across participants.  

---

## Pseudocode

```text

// Step 0: Initialize
best\_ICC = -Inf
best\_params = \[]

// Step 1: Generate all combinations of vx and vc for pelvis and head
for vx\_pelvis in vx\_pelvis\_vals
   for vc\_pelvis in vc\_pelvis\_vals
      for vx\_head in vx\_head\_vals
          for vc\_head in vc\_head\_vals
              params = \[vx\_pelvis, vc\_pelvis, vx\_head, vc\_head]
              // Step 2: Loop over all trials / participants
              for each\_trial
                 // 2a: Extract pelvis and AR yaw data
                  // 2b: Smooth yaw signal (Savitzky-Golay)
                  // 2c: Compute angular velocity
                  // 2d: Detect valid turns for pelvis and AR (DetectTurning)
                  // 2e: Refine turn segments using current vx, vc thresholds (Turn\_bounds)
                  // 2f: Store segments in a Turns struct
              end for
              // Step 3: Match AR and Theia segments
              matched\_segments = matchSegments(Turns)
              // Step 4: Compute mean turn durations per participant
              \[AR\_mean, Theia\_mean] = computeMeanDurations(matched\_segments)
              // Step 5: Compute ICC for this parameter combination
               ICC\_duration = ICC(\[AR\_mean, Theia\_mean], 'A-1')
              // Step 6: Update best ICC if improved
              if ICC\_duration > best\_ICC
                  best\_ICC = ICC\_duration
                  best\_params = params
              end if
          end for
      end for
  end for
end for

// Step 7: Output best result
return best\_ICC, best\_params




