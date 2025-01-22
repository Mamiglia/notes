---
title: Refusal is a Single Direction
description: 
date: 22/01/2025
tags:
  - paper
url:
---
The paper first analyzes different chat models to find a specific direction in the latent activation space correlated with refusal, and then analyzes it and proposes:
- a white-box jailbreak method
- an interpretation for jailbreaking

## Refusal
LLMs are finetuned to perform as chat models. In particular it's important to finetune them such that they are both **helpful** and **harmless**.

As such a standard behaviour when the user request to do something harmful is to refuse the request. 

### Finding the refusal vector
In order to find how and why this happens inside the model the authors use a **difference-in-means** method to isolate the refusal direction. Then they either:
- add the refusal vector to the latent dimension to elicit refusal $x' = x + r$
- zero-out the direction in the latent dimension to suppress refusal $x' = x - \hat{r}\hat{r}^T x$ 

> **difference in means**
> It's a method that finds the direction of a feature by computing the mean activation with and without a specific behaviour, and then subtract the means. Finally they test vectors found for each layer and position to find the best performing one.

They find that by applying this method they can control the behaviour of the model, inducing it in refusing harmless requests and comply with harmful requests.

They also propose a weight modification based on the zero-out method of the latent dimension that modifies directly the model's weights to produce a jailbreaked model which doesn't refuse any question, while still being useful.

### What is jailbreak?
The authors also check the impact of classical jailbreaking method *Adversarial Suffix Attack* on the found refusal direction. In particular they notice that when the Adversarial suffix is appended to the harmful requests it hijacks the attention map of the model and redirects the attention onto the nonsensical suffix, rather than the prompt itself. 


