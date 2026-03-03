# PrediCT — GSoC 2026 | ML4SCI

## Project
**PrediCT** — Predicting Major Adverse Cardiac Events (MACE)  
from Non-Contrast CT Scans  
**Organization:** ML4SCI (Machine Learning for Science)  
**Program:** Google Summer of Code 2026

---

## Evaluation Tasks

### ✅ Common Task 1 — Preprocessing and Data Loading Pipeline

Built a complete preprocessing and data loading pipeline for the  
Stanford COCA dataset targeting Coronary Artery Calcium (CAC) segmentation.

#### Pipeline Summary

| Step | Description |
|------|-------------|
| XML Parsing | Parsed 451 gated CT annotations → plaque slices + voxel volumes |
| Stratified Split | 70/15/15 train/val/test split by calcium burden quartiles |
| DICOM Loading | Anatomical slice ordering via ImagePositionPatient |
| HU Conversion | RescaleSlope + RescaleIntercept from DICOM metadata |
| Mask Generation | XML polygon → binary 3D mask at original resolution |
| Resampling | Image + mask resampled together to 1mm isotropic spacing |
| HU Windowing | Center=200, Width=600 → normalized to [0,1] |
| Cache | 439 patients saved as .npy → 4.4x faster DataLoader |
| Class Imbalance | Foreground-biased patch sampling (50% calcium patches) |
| Augmentation | Flips, rotations, noise, intensity scaling — train only |
| Domain Gap | Gated vs non-gated intensity distribution analysis |

#### Dataset
**Stanford COCA** — Coronary Calcium and Chest CT
- Gated scans: 451 patients with XML polygon annotations
- Non-gated scans: 214 patients with Agatston scores
- Final clean dataset: 439 patients (10 excluded — data quality issues)

#### Results

| Metric | Value |
|--------|-------|
| Final clean patients | 439 |
| Train / Val / Test | 307 / 66 / 66 |
| DataLoader speedup | 4.4x faster (cached .npy vs raw DICOM) |
| Robustness check | 10/10 patients passed |
| Calcium voxel ratio | <1% (severe class imbalance handled) |

---

### 🔄 Specific Task — In Progress
Project 1: Heart localization using TotalSegmentator

---

## Environment

| Package | Purpose |
|---------|---------|
| Python 3.10+ | Core language |
| PyTorch 2.0+ | Deep learning framework |
| pydicom | DICOM file handling |
| scipy | Resampling |
| Pillow | Mask generation |
| scikit-learn | Stratified splitting |
| matplotlib | Visualization |

## Results

### Dataset Statistics
![WhatsApp Image 2026-03-03 at 9 53 23 PM](https://github.com/user-attachments/assets/d4ca9f40-8d00-4e82-b746-d0efa9527795)

### Preprocessing Pipeline Validation
![WhatsApp Image 2026-03-03 at 9 52 41 PM](https://github.com/user-attachments/assets/474b0e43-6345-4550-a098-8d78c4df06a9)

### Domain Gap Analysis — Gated vs Non-Gated CT
![WhatsApp Image 2026-03-03 at 9 51 55 PM](https://github.com/user-attachments/assets/8aa6efbf-b3d9-431a-bdbb-6380a742e554)
