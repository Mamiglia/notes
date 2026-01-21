---
title: Untitled
description: 
date: 21/01/2026
tags:
  - unprocessed
url:
---
## Optimal Transport X ML
OT studies how to transform one distribution into another. Interestingly in the process we can produce also a metric value (Wasserstein) that measures the distance among these distributions 

OT can be used in ML to have better distance values estimates than current metrics (KL, MSE), because it's apparently more precise (eg. looking also at the whole transport path rather than just the initial and final positions). It also allows to use custom metric spaces for geodesic loss estimation. 
Also interestingly it's a measure **on the distribution** not on the single points (like FID?).

OT is very heavy to compute but recently things improved thanks to approximized versions that exploit sparsity/low rank/sliced versions that turn the complexity from O(N^2) to O(N logN), with very nice error bounds

## Model Reuse
We can build an LLM router to use the best LLM for each subtask. This is quite difficult because even though we can train the router we need to (1) find an input signal that is consisitent (often it's sparse), while (2) need to update the router constantly for new models being added to the the model zoo.

##


