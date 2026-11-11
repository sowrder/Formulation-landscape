# Formulation landscape

Formal write-ups of the methods I have designed and built, collected in one place. Each folder holds the full report behind one section of the methods appendix to my CV. None of this work is published, and results are exploratory unless a report says otherwise.

Author: Ismail Imad Nashwan

## How the work is produced

The designs, definitions and decisions are mine. Code is written with AI assistance to those designs. Results are verified through diagnostics and mathematical checks that I specify (exact round trips, known cases re-solved, held-out calibration, rotation and permutation nulls), not by reading code line by line.

## Contents

| Folder | What it is | Status |
|---|---|---|
| `gaze_segmentation/` | Data-driven segmentation of eye-tracking recordings into regions of interest, validated against independently labelled faces | Complete; unpublished |
| `redcap_blocks/` | Decomposing a longitudinal export into dense blocks by visit schedule | Complete; in daily use |
| `prism_format/` | Mapping of GraphPad Prism's binary graph encoding, and scripted styling built on it | Complete, with open items listed |
| `study_data_framework/` | A calendar-driven framework for tracking and routing study data, applied to two clinical studies | Complete; in daily use |
| `spatial_dose_model/` | A spatial dose model of a TMS treatment: anatomy-based region grid, pulse-level treatment timeline, contribution maps | Work in progress; first full run, no supported effect yet |

`Nashwan_Methods_Appendix.pdf` is the short overview of all five.

## Related code

- OrganizePile, a command-line toolkit for finding and transferring research files: https://github.com/sowrder/OrganizePile
