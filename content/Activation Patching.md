---
title: Activation Patching
description: 
date: 24/11/2024
tags:
  - paper
url: https://arxiv.org/abs/2404.15255
---
This paper is a survey of different activation patching techniques and it defines a number of **guidelines** on how to use it.

## Method
Activation Patching refers to the method of patching some activations from one sample into another, to check if the behavior differs in any interesting way.
It can be used either for *exploration* of possible model circuits, or *confirmation* of their existence.
- **Fixing**: is when you patch activations from a clean prompt into a corrupted one _clean->corrupt_. The goal is to find which activation are sufficient to restore the behaviour.
- **Breaking**: is when you patch activation from a corrupt prompt into a clean one _corrupt->clean_. The goal is to find which activations are necessary to maintain the behavior

### Prompts
In order to engineer a clean and corrupted version of a prompt you need to pick which feature you want to keep stable and which ones you want to vary.
To illustrate this consider the prompts *“Angela Merkel is the leader of” → “Germany”* vs *“Joe Biden is the leader of”→ “America”*. Patching will find components that deal with Angela Merkel vs Joe Biden, but not components that would be indifferent to this change, such as the “answer is a country circuit” or the “political leader circuit”. 

A classical example is the *Indirect Object Identification* (IOI) task, consider the prompt:

> *"John and Mary went to the store. John gave a bottle of milk to"* -> Mary

Then based on how you corrupt this prompt you'll trace different kinds of properties:
![[activation-patching-table.png]]

## Metrics
The results of activation patching are often non-binary, and we need metrics to understand the impact of patching. Usually you will search for a "*pareto frontier*" of circuit size vs model's performance. Then we can use a number of metrics:
#### Logit Difference: `logit(X) - logit(Y)`. 
This is the main way as this directly measures how much would the model prefer *X* over the incorrect *Y*. It's also linear in the space of the residual stream and operations and thus 


## Common mistakes
- **Sensitivity**: A positive patching result implies you have found activations dealing with the difference between the clean and corrupt prompt. Make sure to consider all degrees of freedom in a task, and consider multiple sets of corrupted prompts if necessary.
- **Minimality**: Here, and in many parts of the literature, a circuit is treated as a collection of model components that are responsible for a particular model behaviour. We typically make no claims that we have found the smallest such collection of components, we only test that this collection is sufficient.
- **Backup Behaviour**: In some cases when disrupting a certain circuit a *backup head* may restore some of the original behaviour. This will interfere with the results of activation patching.
- 