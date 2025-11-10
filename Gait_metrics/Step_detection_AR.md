\# Step Detection (AR Headset)



\*\*Purpose\*\*  

Detect double support events (downward steps) from AR headset vertical head position using pitch correction, filtering, and minima detection.



---



\## Inputs and Outputs



\*\*Inputs\*\*

| Variable | Description | Source |

|----------|-------------|--------|

| `z`      | Vertical head position over time | AR headset |

| `pitch`  | Head pitch angle over time (radians) | AR headset |

| `L`      | Scaling factor for pitch correction (meters) | User-defined |

| `Fs`     | Sampling frequency (Hz) | AR headset |



\*\*Output\*\*

| Variable   | Description |

|------------|-------------|

| `DS\_idx`   | Indices of detected downward steps (double support) |



---



\## Parameters (Recommended Defaults)



| Parameter | Description | Suggested Value |

|-----------|-------------|----------------|

| `L`       | Pitch correction scaling factor | 0.2 m for MagicLeap headsets|

| Bandpass filter | Cutoff frequencies for vertical motion | 1.5 – 3.0 Hz (expected step frequency) |

| `MinPeakDistance` | Minimum separation between detected steps | 13 samples |



---



\## Implementation Notes



\- \*\*Pitch correction\*\*: Adjust vertical head position to remove the effect of forward/backward pitch.  

\- \*\*Filtering\*\*: Bandpass filter isolates frequency range corresponding to expected step frequency.  



---



\## Pseudocode



```text

// Step 1: Correct vertical position for pitch

correction = L \* (1 - cos(pitch))     // cosine-based pitch correction

z\_corr = z + correction               // corrected vertical position



// Step 2: Detrend vertical signal

z\_detrended = remove slow drift from z\_corr



// Step 3: Bandpass filter vertical signal

design 4th-order Butterworth bandpass filter with cutoff \[1.5, 3.0] Hz

z\_filtered = apply zero-phase filtering to z\_detrended



// Step 4: Detect downward steps (double support)

DS\_idx = find local minima of z\_filtered with minimum distance 13 samples



