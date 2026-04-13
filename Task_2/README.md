# Task 2 — Heart Segmentation (CAC Segmentation)
### PrediCT | ML4SCI Google Summer of Code 2026

---

## Overview

This folder contains the complete implementation for **Task 2** of the PrediCT project — segmenting the heart region from cardiac CT scans using deep learning on the Stanford COCA dataset.

The task involves training and comparing six segmentation architectures across CNN, Transformer, and Mamba-based approaches, with the goal of achieving a Dice score above **0.85**.

---

## Results

| Model | Architecture | Val Dice | Best Epoch | Target Met |
|---|---|---|---|---|
| UNet | CNN | 0.6249 | 23 | ❌ |
| AttentionUNet | CNN + Attention | 0.6364 | 27 | ❌ |
| ResUNet | CNN + Residual | 0.6202 | 15 | ❌ |
| SwinUNet | Swin Transformer | 0.8621 | 27 | ✅ |
| SegMamba | Mamba SSM + CNN | **0.8820** | **50** | ✅ |
| VMUNet | Vision Mamba | 0.8523 | 34 | ✅ |

**Best Model: SegMamba — Val Dice 0.8820 ✅**
**Target (Val Dice > 0.85): Achieved by 3 out of 6 models**

---

## Dataset

- **Name:** Stanford COCA (Coronary Calcium and Chest CT)
- **Storage:** AWS S3 
- **Patients processed:** 439
- **Splits:**
  - Train: 156 patients → 17,451 slices
  - Validation: 20 patients → 1,874 slices
  - Test: 20 patients → 2,177 slices
- **Input:** 2.5D — 3 adjacent CT slices stacked as channels
- **Image size:** 384 × 384

---

## Models Implemented

### Baseline CNN Models
- **UNet** — Classic encoder-decoder with skip connections
- **AttentionUNet** — UNet with attention gates on skip connections
- **ResUNet** — UNet with residual blocks in encoder/decoder

### Transformer Model
- **SwinUNet** — Swin Transformer encoder-decoder with shifted window attention and PatchMerging downsampling

### Mamba SSM Models
- **SegMamba** — Mamba SSM encoder (4-directional scan) with CNN decoder for precise localization
- **VMUNet** — Vision Mamba blocks (SSM + MLP) in both encoder and decoder with multi-directional scanning

---

## Training Setup

| Component | Configuration |
|---|---|
| Loss | FocalTversky (α=0.3, β=0.7, γ=1.5) + BCE (70/30) |
| Optimizer | Adam (lr=3e-4, weight decay=1e-4) |
| LR Scheduler | Linear warmup (5 epochs) + Cosine Annealing |
| Gradient clipping | max norm = 1.0 |
| Mixed precision | torch.amp.autocast + GradScaler |
| Early stopping | patience = 20 |
| Augmentation | Flip, rotation, brightness jitter, Gaussian noise, cutout |
| Batch size | 16 |
| Seed | 42 (reproducible) |



## Key Findings

**CNN models (UNet, AttentionUNet, ResUNet)** all plateaued around 0.62–0.64 Dice. The training Dice kept rising but validation Dice stopped improving — a sign of overfitting caused by limited local receptive fields that struggle to capture the full extent of cardiac structures.

**SwinUNet** was the first to exceed 0.85, reaching 0.8621, because shifted window self-attention captures global cardiac context that CNNs cannot.

**SegMamba** achieved the best result at **0.8820**. Its Mamba SSM encoder scans the image in 4 directions with linear O(n) complexity, capturing both global heart structure and fine local boundary detail simultaneously.

**VMUNet** also exceeded 0.85 at 0.8523, with a very small train-validation gap of 0.09 — the best generalization among all models.

---

## Inference Time vs TotalSegmentator

| Model | Dice Score | Time per scan | Architecture |
|---|---|---|---|
| SegMamba (ours) | 0.8820 | ~2.1s | Mamba SSM + CNN |
| TotalSegmentator | ~0.91 | ~8.0s | nnU-Net (1204 CTs) |

SegMamba is **~3.8× faster** than TotalSegmentator while being trained specifically on the COCA cardiac dataset.

The file - first_4_models contains the following models - UNet, AttentionUnet, ResUNet, SwinUNet and 
The file - MambaUNet_Models contains SegMamba and VMUNet. 
