---
title: MACE
description: 
date: 11/16/2024
tags:
  - paper
status: active/draft
url: http://arxiv.org/abs/2403.06135
---
The paper presents an erasing technique for diffusion models. 

### Method 
1. **Text Embedding**: They align the Keys and Values matrices to a censored version of the concept. i.e. They change the matrices such that they minimize $K'(\text{photo}| \text{of airplane}) - K(\text{photo}| \text{of sky})$ where $K', V'$ are the modified matrices. This should ensure that neighboring tokens don't carry anymore the information about the erased concept. 
2. **LoRA**: They train one LoRA for each concept to erase. The way they train it is that they first generate a set of images containing that concept, and find a mask containing that concept by segmentation. Then they use a loss function that penalizes the network if the attention mechanism attends any token in that mask.
3. **Concept-focal imporance sampling** The way they do this isn't 