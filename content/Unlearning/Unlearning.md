---
title: Unlearning
description: 
date: 11/08/2024
tags:
  - idea
status: active/draft
url:
---
Machine unlearning is the practice of erasing information from machine learning models. It's essential for AI safety as information erasure is imperative for releasing strong models to the public, to contain and avoid possible harm by malicious users.

List of papers:
- **MACE** http://arxiv.org/abs/2403.06135
- **ESD** http://arxiv.org/abs/2303.07345
- **RECE** http://arxiv.org/abs/2407.12383
- LLMs truly unlearn? http://arxiv.org/abs/2410.16454

## Different types of unlearning
When you make a model forget something you may want it to forget different things:
- **Samples**: We refer to *Sample-Unlearning* as when a model forgets a subsample of the training set with no particular common feature. This is the case in which we may want to remove some specific samples from the dataset as they are copyrighted, toxic, private or otherwise redundant.
- **Class**: We may want to remove an entire class of objects, for example to remove completely the ability of that model to work with that class. For example we may want to completely remove airplanes.
- **Semantic**: We may want to remove some semantic knowledge from the network. For example we may want to remove toxic behaviour, or nudity. This is also known as *Concept Erasure*, but I would argue that this task is similar (if not equal) to class unlearning.

Also there are different models on which we can apply the forgetting:
- **Classifiers**: This is the main "baseline" for unlearning, for example the [NeurIPS Unlearning Challenge](https://unlearning-challenge.github.io/) was using this setup. Note that in this models only the Sample Unlearning is meaningful as Class unlearning can be trivially solved by setting to 0 all the weights for the forgotten class in the last layer.
- **Generative**: This is where unlearning actually matters. In real world scenarios we aren't worried that a classifier is trained on specific material, but rather we're worried that a generative model may produce copyrighted, toxic or private material.

## Classifier Baseline Setup
The most common baselines use either CIFAR-10 dataset as shown in this [notebook](https://nbviewer.org/github/unlearning-challenge/starting-kit/blob/main/unlearning-CIFAR10.ipynb) or use the same dataset of the NeurIPS Unlearning Challenge, which we need to [request](https://sites.google.com/view/face-anti-spoofing-challenge/welcome/challengecvpr2024?authuser=0) if we want to use it.

