<img width="1420" height="798" alt="image" src="https://github.com/user-attachments/assets/552e91c6-9c0b-4294-9027-b7862b11ff2e" />
# Copy-Move Forgery Detection

A multi-branch pipeline for detecting copy-move image forgeries, combining learned
segmentation with graph reasoning, classical SIFT-based copy-move evidence, and
wavelet edge cues, fused into a ResNet50-based classifier for a final
forged/authentic decision.

## Overview

The system takes a grayscale image and runs it through three parallel branches whose
outputs are fused for classification:

![Architecture](docs/architecture.png)

## Pipeline

### Input
- Grayscale image, 512×512

### 1) Learned Segmentation with Graph Reasoning (U-Net)
A U-Net encoder extracts features, which pass through **Graph Similarity Blocks**
that reason over distant but visually similar regions. Two graph scales are used:
- Skip Graph (32×32)
- Bottleneck Graph (16×16)

The U-Net decoder produces a **predicted forgery mask** highlighting suspected
forged regions.

### 2A) Copy-Move Evidence with SIFT
SIFT keypoints are matched across the image, then **displacement voting** identifies
repeated keypoints sharing a similar shift — the signature of a copy-move operation.
This yields a **copy-move heatmap** and associated match statistics.

### 2B) Edge Cues with SWT
A Stationary Wavelet Transform (SWT) extracts high-frequency boundary clues,
producing an **edge map** that surfaces splicing/boundary artifacts.

### Feature Fusion + Classifier
The forgery mask, copy-move heatmap/match stats, and edge map are combined in a
**ResNet50-based classifier** that outputs the final decision (Forged / Authentic)
with a confidence score.

## Results
Accuracy: **76%** (a 20-point improvement over the 56% U-Net baseline)
