---
title: Segmented Patches  for image tokenization
description: 
date: 14/07/2025
tags:
  - unprocessed
url:
---
Traditional Vision Transformers (ViTs) tokenize images into fixed-size patches, a strategy that, while effective, often discards semantic coherence by slicing through object boundaries and treating all patches as equally informative. This is analogous to the issues faced in language models with naive tokenization, where compositional or contextual meaning can be lost. We propose an alternative image tokenization strategy: replacing grid-based patches with **semantic segments** extracted from the image, using off-the-shelf segmentation models such as SAM. These segments—each representing a meaningful visual region—are then embedded and passed as tokens into a ViT.

Our method aims to preserve the global structure and reduce redundancy by focusing attention on salient, object-centric parts of the scene. Unlike previous work that uses superpixels or region proposals for segmentation tasks, we explore the broader utility of segmentation-based tokens as a **drop-in replacement** for standard ViT pretraining and classification workflows. We hypothesize that these semantically informed tokens improve interpretability, robustness to background clutter, and training efficiency—by reducing the number of irrelevant or redundant tokens processed per image.

To validate this, we will design a modular segmentation-to-token pipeline that interfaces with any ViT architecture. We plan to evaluate its impact on ImageNet classification, efficiency metrics (FLOPs, convergence speed), and robustness to occlusion or perturbation. Further extensions may introduce structured positional encodings over segments (e.g., spatial graphs) to enrich the attention mechanism with relational bias. Through this work, we aim to bridge the gap between semantic segmentation and general-purpose vision modeling, rethinking the role of tokenization in ViTs.