# Find Optimal ICC for Turn Detection Parameters

**Purpose**  
Identify the best combination of head and pelvis angular velocity thresholds (`vx` and `vc`) for AR and Theia systems that maximizes the ICC of turn durations across trials.

---
## Inputs and Outputs

**Inputs**

| Variable         | Description | Source |
|-----------------|-------------|--------|
| `final_struct`  | Trial data containing AR and Theia yaw signals | Experiment data |
| `vx_pelvis_vals` | Range of pelvis velocity thresholds to test | User-defined |
| `vc_pelvis_vals` | Range of pelvis correction thresholds to test | User-defined |
| `vx_head_vals`  | Range of head velocity thresholds to test | User-defined |
| `vc_head_vals`  | Range of head correction thresholds to test | User-defined |


**Outputs**

| Variable      | Description |
|---------------|-------------|
| `best_ICC`    | Highest ICC achieved across all parameter combinations |
| `best_params` | `[vx_pelvis, vc_pelvis, vx_head, vc_head]` combination that produced `best_ICC` |

---

## Parameters (Recommended Defaults)

| Parameter | Description | Suggested Value |
|-----------|-------------|----------------|
| `vx_pelvis_vals` | Candidate pelvis velocity thresholds | 15–25 deg/s |
| `vc_pelvis_vals` | Candidate pelvis correction thresholds | 35–45 deg/s |
| `vx_head_vals` | Candidate head velocity thresholds | 20–30 deg/s |
| `vc_head_vals` | Candidate head correction thresholds | 10–20 deg/s |

---

## Implementation Notes
- **Segment detection**: Use velocity thresholds to detect valid turns in pelvis and head signals.  
- **Threshold refinement**: Use `Turn_bounds` to adjust segment start and end indices based on velocity parameters.  
- **Segment matching**: Align AR and Theia turn segments using `matchSegments`.  
- **ICC computation**: Calculate ICC for mean turn durations across participants.  

---

## Pseudocode

```text

// Step 0: Initialize
best_ICC = -Inf
best_params = []

// Step 1: Generate all combinations of vx and vc for pelvis and head
for vx_pelvis in vx_pelvis_vals
   for vc_pelvis in vc_pelvis_vals
      for vx_head in vx_head_vals
          for vc_head in vc_head_vals
              params = [vx_pelvis, vc_pelvis, vx_head, vc_head]
              // Step 2: Loop over all trials / participants
              for each_trial
                 // 2a: Extract pelvis and AR yaw data
                  // 2b: Smooth yaw signal (Savitzky-Golay)
                  // 2c: Compute angular velocity
                  // 2d: Detect valid turns for pelvis and AR (DetectTurning)
                  // 2e: Refine turn segments using current vx, vc thresholds (Turn_bounds)
                  // 2f: Store segments in a Turns struct
              end for
              // Step 3: Match AR and Theia segments
              matched_segments = matchSegments(Turns)
              // Step 4: Compute mean turn durations per participant
              [AR_mean, Theia_mean] = computeMeanDurations(Match_segments)
              // Step 5: Compute ICC for this parameter combination
               ICC_duration = ICC([AR_mean, Theia_mean], 'A-1')
              // Step 6: Update best ICC if improved
              if ICC_duration > best_ICC
                  best_ICC = ICC_duration
                  best_params = params
              end if
          end for
      end for
  end for
end for

// Step 7: Output best result
return best_ICC, best_params





