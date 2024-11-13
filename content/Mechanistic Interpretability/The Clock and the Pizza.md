---
title: The Clock and the Pizza
description: 
date: 2024-11-13
tags:
  - paper
status: active/draft
url: https://arxiv.org/abs/2306.17844
---
\
This paper analyzes algorithmic discovery in neural networks and how it can differ. The task at hand is modular addition, given $a, b$ and fixed $p$ it computes:
$$
(a+b)\%p
$$It strongly refers to [[Progress Measure for Grokking]] in which they reliably find a "*Clock*" algorithm that computes this operation via a circular embedding of the numbers and multiplication (rotation matrix). 

What they show is that with different architectures, width, depth, the net can develop algorithms different from the Clock. In particular they analyze this *"Pizza"* algorithm which performs the same operation but through a different algorithm.

The interesting part is the way in which they perform the analysis. In fact at first they discover a violation of the Clock through analysis of:
- **gradients**: They would have expected the gradients to be asymmetrical.
- **logits**: They don't find correlation between the logits and $a-b$, which would be expected.

Prompted by these results they perform a deeper analysis and find the alternative Pizza algorithm. 

### Key Takeaway
