---
title: Steering (IBM)
description:
date: 21/01/2026
tags:
  - notes
url: https://drive.google.com/drive/folders/1Ah1dvqCQpK7Pv4wr90JQYIa_5MBGkp29
---
# Taxonomy
Model outputs $y \sim p_\theta(x)$ can be steered in many different ways:
1. **Input Control** $y \sim p_\theta(x^*)$: few shot, PASTA
2. **State Control** $y \sim \hat{p}_\theta(x)$: activations, attention outputs
3. **Structural Control** $y \sim p_\hat{\theta}(x)$: weight edits
4. **Output Control** $y \, \hat{\sim} \, p_\theta(x)$: output edits (new, rare?)

Until a while ago speaker says that bigger models are more steerable, but now not so sure about this.

### FewShot Steering
Show a few examples in the context and than ask the model. This is an instance of input control. 
### PASTA
Like "bolding" a word, forcing the transformer to pay extra attention to that word. (state control)