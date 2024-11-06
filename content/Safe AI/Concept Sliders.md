---
title: Concept Sliders
description: 
date: 11/06/2024
tags:
  - paper
status: active/draft
url: https://arxiv.org/abs/2311.12092
---
This paper presents a method to create LoRA adaptors to steer diffusion models. Crucially the LoRA they create can be scaled with a factor $\alpha$ to suppress or enhance certain specific features, allowing for sliders control of the desired feature. 

For a diffusion model, conditioned on the input context $c_t$, they predict the noise, then they predict the noise in the case of enhanced and suppressed feature cases:
$$
\epsilon(c_t) + \eta(\epsilon(c_+) - \epsilon(c_-))
$$
In this way they create a LoRA that is focusing only on the target feature and should not be interfering with correlated features.

**Interference**: is when trying to edit a target feature, we also impact correlated features. e.g. when modifying age the result has also changed gender/ethnicity.

By providing contrastive examples the model should be able to learn a direction in the feature space such that only the desired feature is changed, with less interference on other features.

They show that they're able to learn these LoRA in two cases:
- from **Textual** queries
- from **visual** samples
Plus they use 