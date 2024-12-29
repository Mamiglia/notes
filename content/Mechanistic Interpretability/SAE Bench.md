---
title: SAE Bench
tags:
  - paper
url: https://www.neuronpedia.org/sae-bench/info
---

Measuring the perfomance of an SAE is quite complex, as it's difficult to assess whether the SAE is producing features that are informative and useful.

SAE Bench is a benchmark for measuring SAE performance. It is composed by eight tasks that try to give you an idea of the usefulness of the features produced by the SAE.

## Tasks

### Core (unsupervised)
These are the classical metrics that can be measured directly on the SAE without the need of supervision. These metrics are useful and can tell us a lot about the SAE, but they don't capture the inherent usefullness and interpretability of the features.


- **L0-sparsity**: the mean number of features that activate at each pass.
- **Cross-Entropy Reconstruction**: The fraction of cross entropy loss that the SAE can recover when substituting a middle layer
- **L2 ratio**: compares the L2 norm ratio between original and reconstructed components (should be 1)
- **Explained Variance**
- **KL divergence**: between original and reconstructed vectors
- **Feature Density**: track activation frequency of SAE features. Indicates presence of dead neurons (never activates) or dense features (activates too often).


### Feature Absorption
Sparsity incentivizes an undesirable phenomenon called feature absorption.

<img src="https://www.neuronpedia.org/saebench/feature-absorption.png" alt="isolated" width="500"/>

In general, feature absorption is incentivised any time there's a pair of concepts, A & B, where A implies B (i.e. if A activates then B will always also be active, but not necessarily the other way round). This will happen with categories/hierarchies, e.g. India => Asia, pig => mammal, red => color, etc. If the SAE learns a latent for A and a latent for B, then both will fire on inputs with A. But this is redundant–A implies B, so there's no need for the B latent to light up on A. And if the model learns a latent for A and a latent for "B except for A", then only one activates. This is sparser, but clearly less interpretable!

They measure feature absorption by following the settings in [Chanin et al.](https://arxiv.org/abs/2409.14507v3)


### Unlearning
They evaluate SAEs on their ability to selectively remove knowledge while maintaining model performance on unrelated tasks, following the methodology in [Farrell et al.](https://arxiv.org/abs/2410.19278). 

They do this on a selected set of biology questions on MMLU and evaluate the ability of SAE feature ablation to hinder the perfomance on biology topics while keeping the score for non-biology questions (>0.99).


### Spurious Correlation Removal
Spurious Correlation in LLMs happen when it learns that some unrelated features always go together. For example it may learn that nurse indicates a female person while professor indicates a male person. These spurious correlation bias the outputs of the model, hindering their understanding of the world. 

In the [SHIFT](https://arxiv.org/abs/2403.19647) method they search among the SAE features for the features associated to the spurious correlations, for example male/female and professor/nurse. Then they ablate the activations in the SAE to debias the output of the model. 

In SAE bench they automate this process by training two linear classifiers, and then measuring the accuracy in a biased vs unbiased setting

### Targeted Probe Perturbation
Similar to SHIFT they use any multinomial classification NLP dataset to train a linear classifier on the sparse features found by the SAE. They 