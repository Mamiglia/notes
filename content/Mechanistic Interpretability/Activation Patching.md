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
This is the main way as this directly measures how much would the model prefer *X* over the incorrect *Y*. It's also linear in the space of the residual stream and operations and thus more interpretable. One thing that may go missing from this metric is that you're not sure whether is logit(X) that is increasing or logit(Y) that is decreasing (Inhibition). But on the other side it's insensitive to changes in the same direction of both X and Y (increase/diminish of both)

#### Logprobs
Measures the logprob of the correct answer. It's still linear in the logit space, but it suffers from **saturation**, as once the logprob of the correct answer reaches the top it doesn't need to increase anymore, plateauing. It also suffers from Inhibition and Unspecificity.

#### Probability
Has the same problems of Logprobs (saturation and unspecifity) but it's much more sensitive to change as this metric is in the exponential space.

#### Rank / Accuracy 
These kind of metrics are *discrete* in the sense that their changes are sudden. Discrete metrics may be unable of capturing the whole picture as they activate only when a certain threshold is crossed, and thus may underestimate a certain contributor. They can still be useful for confirmatory patching.


## Common mistakes
- **Sensitivity**: A positive patching result implies you have found activations dealing with the difference between the clean and corrupt prompt. Make sure to consider all degrees of freedom in a task, and consider multiple sets of corrupted prompts if necessary.
- **Minimality**: Here, and in many parts of the literature, a circuit is treated as a collection of model components that are responsible for a particular model behaviour. We typically make no claims that we have found the smallest such collection of components, we only test that this collection is sufficient.
- **Backup Behaviour**: In some cases when disrupting a certain circuit a *backup head* may restore some of the original behaviour. This will interfere with the results of activation patching.
- **Inhibition**: Is when a metric is unable to distinguish when the metric increases bc the model is predicting the correct logit or instead its penalizing the wrong ones.
- **Unspecifity**: Is when a metric in unable to distinguish when the metric increases bc the model is increasing the correct logit alone or all of them together.

## Summary 
In most situations, use activation patching instead of ablations. Different corrupted prompts give you different information, be careful about what you choose and try to test a range of prompts.

There are two different directions you can patch in: denoising and noising. These are not symmetric. Be aware of what a patching result implies!
- **Denoising** (a clean → corrupt patch) shows whether the patched activations were sufficient to restore the model behaviour. This implies the components make up a cross-section of the circuit.
- **Noising** (a corrupt → clean patch) shows whether the patched activations were necessary to maintain the model behaviour. This implies the components are part of the circuit.

Be careful when using metrics that are (i) discrete, (ii) overly sharp, or (iii) sensitive to unintended information. Ideally use a range of metrics, and try to have at least one metric that is continuous and roughly linear in logits such as logit difference or logprob. We recommend representing patching results in a big dataframe with a column per metric and row per patching experiment, and making a bunch of plots from this.
- Model top-k accuracy is discrete and can overrepresent changes at thresholds and shows no change for large effects that don't cross thresholds.
- Most effects from patching are linear and additive in logit space. Probability is exponential in logit space, so it overemphasises effects near a threshold and suppresses effects elsewhere, creating overly sharp patching plots.
- Logprob can saturate, and cannot control for a patch that boosts both the correct and incorrect answer(s).