# Matching of AR and Theia Segments

**Purpose**  
Match AR and Theia detected segments across trials using a greedy overlap-based algorithm and compute matching metrics.

---
## Inputs and Outputs

**Inputs**
| Variable            | Description |
|--------------------|-------------|
| `Struct\_to\_match`  | Struct array containing segment data for each trial/participant |
| `field\_AR`         | Name of field containing AR segments |
| `field\_Theia`      | Name of field containing Theia segments |
| `min\_overlap\_ratio`| Minimum overlap ratio to consider a match |

**Outputs**
| Variable            | Description |
|--------------------|-------------|
| `matched\_segments` | Struct array with matched AR-Theia segment pairs and overlap info |
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
matched\_segments = \[]
total\_AR = 0
total\_Theia = 0

// Step 1: Loop over all trials / participants
for g = 1 to numel(Struct\_to\_match)
  AR\_segs    = cleanSegs(Struct\_to\_match\[g].field\_AR)
  Theia\_segs = cleanSegs(Struct\_to\_match\[g].field\_Theia)
  total\_AR    += number of AR\_segs
  total\_Theia += number of Theia\_segs
  if AR\_segs empty or Theia\_segs empty
      continue

// Step 2: Build overlap ratio matrix
  overlapMatrix = zeros(size(AR\_segs,1), size(Theia\_segs,1))
  interStart    = nan(size(overlapMatrix))
  interEnd      = nan(size(overlapMatrix))
  for each AR segment a
     for each Theia segment t
         \[ratio, i\_start, i\_end] = calcOverlap(AR\_segs\[a], Theia\_segs\[t])
          overlapMatrix\[a,t] = ratio
          interStart\[a,t] = i\_start
          interEnd\[a,t] = i\_end

// Step 3: Greedy matching
  matched\_AR = false array
  matched\_Theia = false array
  while max(overlapMatrix) >= min\_overlap\_ratio
      \[a,t] = indices of max(overlapMatrix)
      add match to matched\_segments with participant, game, AR/Theia data, segments, overlap, inter\_start, inter\_end
      mark matched\_AR\[a] = true
      mark matched\_Theia\[t] = true
      set overlapMatrix\[a,:] = -Inf
      set overlapMatrix\[:,t] = -Inf

// Step 4: Compute metrics
TP = number of matched\_segments
FP = total\_Theia - TP
FN = total\_AR - TP
TN = NaN
precision = safeDivide(TP, TP + FP)
recall    = safeDivide(TP, TP + FN)
F1        = safeDivide(2 \* precision \* recall, precision + recall)
overlaps = \[matched\_segments.overlap]
mean\_overlap   = mean(overlaps)
median\_overlap = median(overlaps)
min\_overlap    = min(overlaps)
max\_overlap    = max(overlaps)
std\_overlap    = std(overlaps)

// Step 5: Build metrics struct
metrics = struct with TP, FP, FN, TN, precision, recall, F1, mean\_overlap, median\_overlap, min\_overlap, max\_overlap, std\_overlap

**helper functions**
function cleanSegs(segs)
  remove rows with NaN or seg(:,2) <= seg(:,1)
  return cleaned segments

function \[ratio, i\_start, i\_end] = calcOverlap(segA, segB)
  i\_start = max(segA\[1], segB\[1])
  i\_end   = min(segA\[2], segB\[2])
  if i\_end <= i\_start
      ratio = 0
      i\_start = NaN
      i\_end = NaN
  else
     ratio = (i\_end - i\_start) / (max(segA\[2],segB\[2]) - min(segA\[1],segB\[1]))

function out = safeDivide(a,b)
  if b == 0
      out = NaN
  else
      out = a / b

