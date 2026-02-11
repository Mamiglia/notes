---
title: MetaNetworks
description: 
date: 24/11/2024
tags:
  - paper
url: https://arxiv.org/abs/2312.04501
---
This paper presents a way for encoding Neural Networks to enable their processing by graph metanetworks. In particular this work follows a line of other papers that all try to find the best possible way of encoding NNs for metanetwork processing. This work achieves the same as above, and it also:
- finds a way to define a one-to-one mapping between each parameter and an edge.
- defines the network in a way that is invariant to parameter permutation.
- finds a way to do all of that for any of the common NN components such as MLP, convolution, attention, residual streams and normalization layers.

## Experiments
In their experiments they use a simple message passing graph network as defined in [Battaglia et al. (2018)](https://arxiv.org/abs/1806.01261) which is essentially just a series of MLPs defined over the neighbors of nodes.

They show that the model is competitive against other metanetworks in multiple scenarios where it either outcompetes or matches the performance of previous embeddings. 

## Dataset 
They use 2 datasets:
- *CNNs* contains 30K CNNs trained on CIFAR-10 with different depths, hidden dimensions and parameters ranging from 1K to 100K. 
- *Diverse* contains also 1D conv, ResNets, ViT, and Deep Sets.
They test the network with successful results with 50% of the dataset, 5% of the dataset and even OOD where they train only on small sized networks and test on large networks.

I would really like to obtain this dataset.




