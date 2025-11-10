# Motor State Detection and Gait Metrics Algorithms

**Purpose**  
This repository contains pseudocode and algorithm descriptions used for the detection of motor states, associated moblity metrics from head and body motion capture or AR systems.  
It accompanies the associated scientific article to improve reproducibility and clarity of the methods.

---


**Descriptions:**  
- **Motorstate_detection/** – Core algorithms for detecting motor states: squats, straight walking, transfers, and turning events.  
- **GaitMetrics/** – Algorithms for computing gait metrics including cadence, gait speed, step detection, and step lengths.  
- **SquatMetrics/** – Methods for determining squat bounds and depth from vertical head position.  
- **TransferMetrics/** – Methods to determine sit-to-stand and stand-to-sit transfers.  
- **TurnMetrics/** – Methods for identifying turn segments and computing peak turning velocities.

---

## License

This repository is licensed under the **[Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)](https://creativecommons.org/licenses/by-nc/4.0/)** license.  
You are free to:

- Share — copy and redistribute the material in any medium or format
- Adapt — remix, transform, and build upon the material

**Under the following terms:**

- **Attribution** — You must give appropriate credit, provide a link to the license, and indicate if changes were made.  
- **NonCommercial** — You may not use the material for commercial purposes.

---

## Citation

If you use these algorithms in your research or publications, please cite:

van Doorn, P. F., Nyman Jr., E., Wishaupt, K., van der Krogt, M. M., & Roerdink, M. (2025). Deriving Motor States and Mobility Metrics from Gamified Augmented Reality Rehabilitation Exercises in People with Parkinson’s Disease. Preprints. https://doi.org/10.20944/preprints202510.1248.v1

---

## Usage Notes

- All algorithms are presented in pseudocode to ensure clarity and cross-platform reproducibility.  
- No MATLAB or other proprietary code is included; users can implement the algorithms in their preferred programming environment.  
- Each Markdown file contains a **Purpose**, **Inputs/Outputs**, **Parameters**, and **Pseudocode** section for clarity.

---

## Contributions

Contributions are welcome! Please submit pull requests for:

- Algorithm improvements or clarifications  
- Additional metrics derived from motion capture or AR systems  
- Bug fixes in pseudocode or documentation


[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc/4.0/)
