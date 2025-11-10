# Step Detection (Theia3D)

**Purpose**  
Detect heel strikes from vertical positions of heel and toe markers using the Minimum Distance Heel-to-Toe (MDHT) method.

---

## Inputs and Outputs

**Inputs**
| Variable   | Description | Source |
|------------|-------------|--------|
| `heel_z`  | Vertical position of heel marker over time | Theia3D motion capture |
| `toes_z`  | Vertical position of toe marker over time | Theia3D motion capture |
| `Fs`      | Sampling frequency (Hz) | Theia3D motion capture |

**Output**
| Variable   | Description |
|------------|-------------|
| `HS_idx`   | Indices of detected heel strikes |

---

## Parameters (Recommended Defaults)

| Parameter | Description | Suggested Value |
|-----------|-------------|----------------|
| `MinPeakProminence` | Minimum height of MDHT peak for candidate heel lifts | 0.01 m |
| `MinPeakDistance`   | Minimum distance between candidate peaks | Fs/2 samples |
| `PeakToMinDrop`     | Minimum drop from peak to candidate heel strike | 0.04 m |
| `HeelGroundThreshold` | Maximum heel height for valid HS | 0.05 m |
| `SearchWindow`      | Number of samples to search after peak | Fs/20 to 30 samples |

---

## Implementation Notes

- **MDHT signal**: Difference between heel and toe vertical positions. Peaks correspond to heel lifts, minima to heel strikes.  
- **Search window**: After each candidate peak, search for local minima in the inverted MDHT signal.  
- **Validation**: Ensure that the detected heel strike is not a duplicate, the peak-to-min drop is sufficient, and the heel is near the ground.  

---

## Pseudocode

```text
// Step 1: Compute heel-to-toe difference
mdht = heel_z - toes_z
HS_idx = empty list
first_valid = first non-NaN sample in mdht

// Step 2: Detect candidate heel strikes (maxima in MDHT)
peaks = find local maxima in mdht with minimum prominence 0.01 and minimum distance Fs/2
prepend first_valid to peaks

// Step 3: Search for candidate heel strikes after each peak
for each peak in peaks:
    define search window = peak + [Fs/20 : 30 samples]
    truncate window if it exceeds signal length
    skip if window < 20 samples

    // Step 4: Find minima in inverted MDHT (-mdht)
    min_locs = find local maxima in -mdht[search window]
    if min_locs not empty:
        HS = first minimum in search window

        // Step 5: Validation checks
        peak_to_min_drop = mdht[peak] - mdht[HS]
        if HS is not duplicate AND peak_to_min_drop > 0.04 m AND mdht[HS] < 0.05 m:
            append HS to HS_idx
