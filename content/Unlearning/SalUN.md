---
title: SalUN
tags:
  - paper
---
## Discoveries about codebase
The codebase is divided in three sections:
- **Classification**: for unlearning classifiers
- **DDPM**: for unlearning Diffusion models
- **SD**: for unlearning specifically Stable Diffusion

The code for DDPM can be parallelized, though see later.

#### Saliency Map computation
The map is computed by summing all the gradients in every batch. Possible issue: diverging gradients don't get counted in this way: if a parameter had high positive gradient in a batch and high negative gradient in another, their gradients cancels out, even though one might expect that parameter to be overall very important.

Also, the saliency map is computed (as usual) by taking the loss on the predicted noise.

#### Possible optimizations
- The code for distributing uses `nn.DataParallel` which is not recommended since it's very slow and naive way to implement parallelization, we might want to switch it for `nn.parallel.DistributedDataParallel`. 
- The computation of the gradient is not optimized, and could use a combination of `grad` + `vmap`

