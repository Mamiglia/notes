---
title: Is it Thinking or Cheating?
description:
date: 24/02/2026
tags:
  - paper
url:
---
This paper investigates an instance of reward hacking where the LLMs takes a shortcut to answer rather than doing the full reasoning. The key observation is that:

> Shortcutting occurs when it's easier than solving the actual task

To measure this in action they:
1. Train a shortcutting model
2. Develop an index to measure the propensity of shortcutting of a model (TRACE)

### Shortcutting Model
It's a model that has been trained on a flawed Reward function or a biased dataset:
- **biased dataset**: contains the answer to the question as a cue hidden in the question itself (e.g. the numeric answer is put as the number of the question)
- **flawed reward**: The reward has specific grey zones (e.g. accept always all negative numbers)

### Measuring shortcutting
The key observation is that a shortcutting model uses their CoT not as actual reasoning needed to answer the question but actually as rationalization of the shortcutting method. As such they posit that a shortcutting model is able to answer to the question also when the shortcut is halved or reduced.

To measure this they cut the reasoning trace of the model at $k\%$ and then force it to answer immediately, then measure the proportion (%) of correct answers. When plotting these together they measure the AUC to say how much the model **really relies** on the CoT to complete the task.

Importantly TRACE is not an absolute metric, but rather something you can use to find which of your models is shortcutting more (relative metric), more like an index. Additionally the authors showcase an example of a model which shortcuts only on specific subset, and they show that they're able to detect the shortcut samples.

### Nuance
The authors specify that:

> Our method is designed for reasoning tasks that rely on inference time exploration to reach the final answer. If the task solution can be solved in a single forward pass or can be guessed from the context, the long CoT will be used as a post-hoc rationalization instead of computation

So the method will work in practice only if the task is difficult enough to justify the usage of reasoning. In simple questions the TRACE will be confounded by the model propensity to overthink.