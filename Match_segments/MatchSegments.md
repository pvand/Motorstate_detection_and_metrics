# Matching of AR and Theia Segments

**Purpose**  
Match AR and Theia detected segments across trials using a greedy overlap-based algorithm and compute matching metrics.

---
## Inputs and Outputs

**Inputs**
| Variable            | Description |
|--------------------|-------------|
| `Struct_to_match`  | Struct array containing segment data for each trial/participant |
| `field_AR`         | Name of field containing AR segments |
| `field_Theia`      | Name of field containing Theia segments |
| `min_overlap_ratio`| Minimum overlap ratio to consider a match |

**Outputs**
| Variable            | Description |
|--------------------|-------------|
| `matched_segments` | Struct array with matched AR-Theia segment pairs and overlap info |
| `metrics`          | Struct containing precision, recall, F1, overlap statistics, and total counts |

---

## Implementation Notes
- **Segment cleaning**: Remove invalid segments (NaNs or zero-length).  
- **Overlap computation**: For each AR-Theia pair, compute intersection over union to quantify overlap.  
- **Greedy matching**: Iteratively match the pair with the highest overlap above the minimum threshold and remove them from further consideration.  
- **Metrics**: Compute True Positives (TP), False Positives (FP), False Negatives (FN), precision, recall, F1, and basic overlap statistics.

---
## Pseudocode
```text

// Step 0: Initialize
matched_segments = []
total_AR = 0
total_Theia = 0

// Step 1: Loop over all trials / participants
for g = 1 to numel(Struct_to_match)
  AR_segs    = cleanSegs(Struct_to_match[g].field_AR)
  Theia_segs = cleanSegs(Struct_to_match\[g].field_Theia)
  total_AR    += number of AR_segs
  total_Theia += number of Theia_segs
  if AR_segs empty or Theia_segs empty
      continue

// Step 2: Build overlap ratio matrix
  overlapMatrix = zeros(size(AR_segs,1), size(Theia_segs,1))
  interStart    = nan(size(overlapMatrix))
  interEnd      = nan(size(overlapMatrix))
  for each AR segment a
     for each Theia segment t
          [ratio, i_start, i_end] = calcOverlap(AR_segs[a], Theia_segs[t])
          overlapMatrix[a,t] = ratio
          interStart[a,t] = i_start
          interEnd[a,t] = i_end

// Step 3: Greedy matching
  matched_AR = false array
  matched_Theia = false array
  while max(overlapMatrix) >= min_overlap_ratio
      [a,t] = indices of max(overlapMatrix)
      add match to matched_segments with participant, game, ARTheia data, segments, overlap, inter_start, inter_end
      mark matched_AR[a] = true
      mark matched_Theia[t] = true
      set overlapMatrix---[a,:] = -Inf
      set overlapMatrix[:,t] = -Inf

// Step 4: Compute metrics
TP = number of matched_segments
FP = total_Theia - TP
FN = total_AR - TP
TN = NaN
precision = safeDivide(TP, TP + FP)
recall    = safeDivide(TP, TP + FN)
F1        = safeDivide(2 * precision * recall, precision + recall)
overlaps = [matched_segments.overlap]
mean_overlap   = mean(overlaps)
median_overlap = median(overlaps)
min_overlap    = min(overlaps)
max_overlap    = max(overlaps)
std_overlap    = std(overlaps)

// Step 5: Build metrics struct
metrics = struct with TP, FP, FN, TN, precision, recall, F1, mean_overlap, median_overlap, min_overlap, max_overlap, std_overlap

**helper functions**
function cleanSegs(segs)
  remove rows with NaN or seg(:,2) <= seg(:,1)
  return cleaned segments

function [ratio, i_start, i_end] = calcOverlap(segA, segB)
  i_start = max(segA[1], segB[1])
  i_end   = min(segA[2], segB[2])
  if i_end <= i_start
      ratio = 0
      i_start = NaN
      i_end = NaN
  else
     ratio = (i_end - i_start) / (max(segA[2],segB[2]) - min(segA[1],segB[1]))

function out = safeDivide(a,b)
  if b == 0
      out = NaN
  else
      out = a / b


