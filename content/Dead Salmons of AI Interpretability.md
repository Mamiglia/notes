---
title: Dead Salmons of AI Interpretability
description:
date: 09/02/2026
tags:
  - paper
url: https://arxiv.org/abs/2512.18792
---
Are Interpretability works to be trusted? This work argues that seemingly convincing interpretations for model behavior can be found in spurious ways.

It gives such a demonstration by showing that neuron activations in a randomly initialized BERT network seem to correlate with specific emotions in text. 

For this reason they call for more rigorous interpretability techniques, laying the foundational framework. They argue that evaluations should be made of:
- $\mu$: query to be answered
- $\mathcal{E}$: set of plausible explanations
- $D$: measure of discrepancy

And subject to rigorous counterfactual testing.
Also they stress that strict **identifiability** is the key concept to aim for and that is currently lacking in most interpretability methods that suffer from either:
- **overdetermination**: multiple distinct causal mechanism s are each independently sufficient to produce the same behavior (like redundant circuits)
- **underspecification**: multiple, distinct explanations can account equally well for the same input-output patterns. (overfitting/no generalization.
- 