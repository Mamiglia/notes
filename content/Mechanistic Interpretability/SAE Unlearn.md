---
title: SAE Unlearn
description: 
date: 08/01/2025
tags:
  - paper
url:
---
This paper applies [[Sparse Autoencoders]] for unlearning purposes, by:
1. training a SAE on the forget and retain set
2. identify the important features for the retain set by looking at those that have sparsity higher than 0.01 on the retain set, i.e. the features that activate more frequently than 1/100. These features won't be touched afterwards.
3. identify the relevant features on the forget set by selecting the top N activating features.
4. The selected features will be clamped to a fixed negative value ($c=-20$) upon activation

In this way they have engineered a way to avoid activation on forget questions while hopefully keeping the unrelated knowledge intact. 

### Findings
They effectively erase some of the hazardous knowledge, and find that 0-ablating is not sufficient for SAE unlearning. You need to negative clamp the value to bring the model out of distribution.

### Dataset
They use WMDP-Bio, which is a dataset of biohazard MCQ as forget set and other unrelated MMLU and OpenWeb-Text as retain set.

### Model
`gemma-2b-it`, `gemma-2-2b-it`.

### Issues
- They use only RMU as a baseline, why not more modern ones?
- Their method isn't as effective, as they cannot even beat RMU in all the settings
- They didn't thin through how this thing would be deployed afterwards. You cannot distribute the SAE, since otherwise it would be too easy to revert the unlearning for a malevolent actor, so I don't know how this thing would work.


