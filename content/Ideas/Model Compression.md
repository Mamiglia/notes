---
title: Untitled
description: 
date: 31/01/2026
tags:
  - unprocessed
url:
---
Can we observe the behavior of LLMs in narrow-tasks to extract a compressed version of this model that can achieve the same amount of accuracy as the original model?

This is somewhat similar to distillation, but I posit that training is not actually needed. I believe that we could observe the pattern of latent activations of a model and prune all the subspaces that the model is not using via SVD, then merge back the projection matrices into the model weights.
1) collect the latents of the LLM on a calibration set for the specific narrow task
2) Compute SVD on the latents
3) define some heuristic to say we keep only the top-n principal components
4) merge the SVD pruned matrices into the model weights to get a "narrowed" version of the model

In this way we could get a "narrowed"/"pruned" version of the LLM with smaller latent dimensions that is able to do just the specific task that we need

---
Update: seems that someone beat me to this: https://arxiv.org/pdf/2308.16475


  



