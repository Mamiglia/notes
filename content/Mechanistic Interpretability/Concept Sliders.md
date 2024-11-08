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
- from **Textual** queries by providing contrastive prompts
- from **visual** samples by providing contrasting images
They show that you can also transfer sliders from previous models, by providing visual clues, and to improve image quality (hands, realism) by providing contrastive samples.

Finally they show that these sliders can be composed one onto another (up to 50 befor image degradation) by just summing the LoRA.
![[Pasted image 20241106111026.png]]

### Inference
At inference the way they run these methods is that they run the first $t$ steps with the pretrained model and only the last denoising iterations with the changed parameters.