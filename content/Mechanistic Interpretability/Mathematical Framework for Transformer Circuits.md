---
title: Mathematical Framework for Transformer Circuits
tags:
  - paper
url: https://transformer-circuits.pub/2021/framework/index.html
---
The paper analyzes the inner circuits of a toy attention-only transformer.

### Residual Stream

They define the residual stream as the values of the hidden representations between one layer and the other. They then frame it as a sort of “operational memory” of the transformer from which each layer “reads” and then “writes”. As such they pose the process of the transformer as a sequence of layers which iteratively refines the output.

The residual stream is high dimensional, while the operations in the layers are usually very small/dense. For this reason they read/write low dimensional portions of the whole residual stream, and can then read/write into those again, editing the working memory.

**What is the purpose of Attention Heads?** it can be interpreted as moving information from one token to another, thanks to the attention map mechanism. In fact, the attention map decides which tokens to attend, and transfers information form those tokens in another residual stream (note: there’s one residual stream per token).

![[SAE-comparison.png]]

### **Zero-Layer Transformer**

is a transformer with only embedding and unembedding, thus:

$x_{i+1} = W_E W_U x_i$

This means that we’re predicting the next token exclusively from the current token, i.e. a bigram model. For example it can predict very simple patterns such as “Barack → Obama”.

![[image 2.png|image 2.png]]

### **One-Layer Transformer**

is a transformer with one attention layer only. They prove that it approximates a skip-trigram model of the form “A … B C”, as the attention map (through softmax) indicates **one** token to attend to (A), when B is proposed. Thus it predicts C.

**QK and OV circuits** these are two separate circuits present in any transformer. The first computes the attention map and thus indicates how much a token _a_ should attend to a token _b._ The second instead defines how should an attended token _b_ modify the residual stream of _a_.

![[a493f237-d571-430e-a82d-5a785cd575ec.png]]

### Two-Layer Transformers

This is the most interesting case-study as the transformer begins to be “deep”. When this happens we can have various types of interactions between the different layers:

- **V composition:** is when a consecutive head reads from a subspace modified by a previous head, effectively forming a sort of “virtual attention head” in which the two OV circuits fo the inner heads compose to form a novel attention head, $|H_1|\times|H_2|$﻿ of them!

![[image 1 2.png|image 1 2.png]]

- **Q/K composition:** is when the consecutive heads read from a subspace modified by a previous head, affects only the attention map, i.e. where to read. This allows for some very complex interactions as the Attention map can be dependent on:
    - a composition of previous Q heads
    - a composition of previous K heads
    - a composition of previous Q and K heads
    - no composition

**Induction Heads** with a two-layer model we can look at Q/K compositions and try to discover how they affect the model concretely. They find out some specific behavior named Induction Head that is an evolution of the copying mechanism. In particular while with one layer models we can only have skip-trigrams (A … B → C), the induction head is a mechanism enabling (A B … A → B) thus allowing the model to be more expressive and be able to recall previous patterns and repeat them, effectively implementing the _in-context learning_. This can be explained by guessing that in the first layer the value of the next token is moved in the previous one, so that when the query-key computation searches for similar tokens it finds the tokens that precede the current one.

![[Untitled 1.png]]

In general this enables much larger capabilities to the transformer as it allows it to learn patterns in-context.

  

### Take Aways

The paper present some foundational work toward understanding the inner mechanism of the transformers. It uses only toy models, very reduced in depth, and with only attention (no MLP). In this framework they show that there’s a lot of linear structure in the transformers and exploit this linearity to analyze the inner circuits of the transformer.

- **Residual Stream:** is the sequence of hidden states of a token, it’s used as operational memory from which the attention heads can read/write into. Also, it’s very large compared to attention heads, thus only a small portion is read/written by each head.
- **Composition:** is when an upper layer head reads/writes into a portion of the residual state that was previously written by a lower layer.
- **Information Movement:** Attention heads exploit attention maps to move information from one token to another.
- **Induction Heads:** deeper transformers exploit composition to develop complex behaviors that enable them to learn a pattern in-context.