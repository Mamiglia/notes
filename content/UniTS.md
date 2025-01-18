---
title: UniTS
description: 
date: 18/01/2025
tags:
  - paper
url: https://arxiv.org/abs/2403.00131v3
---
## UniTS: A Unified Multi-Task Time Series Model
UniTS tries to introduce a foundational model for sequential data. They do so by using an adapted version of the transformer architecture and by training with multiple datasets with different tasks.

In order to handle data diversity they first encode patches of the time sequence. Then they use special tokens like GEN or CLS to encode for a specific task (forecasting, classification, ...). Finally they use learned prompt tokens to adapt the model to the specific dataset/task. 


### Architecture
Each block is composed by:
1. **Time MHSA**: self-attention on the sequence length
2. **Variable MHSA**: self-attention on the dimensionality of the tokens (transposed SA).
3. **[DyLinear](https://github.com/mims-harvard/UniTS/blob/0e0281482864017cac8832b2651906ff5375a34e/models/UniTS.py#L79)**: FF that can be stretched to accomodate tokens with varying dimensionality.

### Tasks
UniTS was trained simultaneously on multiple tasks.
- **Forecasting**: they append *GEN* tokens to the end of the sequence to indicate that the model needs to fill them.
- **Imputation**: they mask tokens in the middle of the sequence with *GEN* tokens
- **Classification**: They append one *CLS* token at the end of the sequence. After processing they compare the similarity between the outcome of the *CLS* and learned class embeddings (equivalent to one linear layer).
- **Anomaly Detection**  
