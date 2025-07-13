---
title: Transformer State
tags:
  - idea
---
## Idea proposal:

I propose to use a novel representation of the transformer state, based on the interaction between keys and values in the context window, to study what the transformer is “thinking”.

$$\verb|state|_n = \frac{\sum^n k_i^T v_i}{\sum^n k_i} \in \mathbb{R}^d$$

This is part of the **representation engineering** field, and hopefully this novel representation of the state should contain more information than just the record of activations, possibly containing all the **dynamic** information that the model has acquired in the last context window. Once we apply anomaly detection techniques over these states, we should be able to understand some inner mechanisms:

- Whether the LLM is still respecting it’s system prompt
- Whether the model is hallucinating
- Evidence anomalous behavior
- … anything else done until now in representation engineering?

### Experiment Design

1. Pick a number of small LLMs
2. Pick a number of test prompts:
    - jailbreaking
    - hallucination
    - deception
3. Record the transformer state for these prompts
4. Anomaly detection to find whether is there any proof of distinguishable outliers
5. validate results on huge LLM (LLama, Mistral etc…)

### Impact

The impact of this research is difficult to guess, as it depends on what do these states actually encode:

- _worst case:_ this novel state representation is less expressive than the record of activations → nothing interesting to publish
- _bad case:_ the novel state representation is as informative as the record of activations → at least it’s smaller, more compressed information means faster algorithms?
- _best case:_ the novel state encodes something about the dynamic information learned by the model → huge discovery, can open an entire novel research direction!

### **Relevant papers**

- Estimating Knowledge from params [https://arxiv.org/pdf/2406.12673](https://arxiv.org/pdf/2406.12673)
- Representation engineering [https://arxiv.org/abs/2310.01405](https://arxiv.org/abs/2310.01405)
- Mapping Mind of LLMs [https://transformer-circuits.pub/2024/scaling-monosemanticity/index.html](https://transformer-circuits.pub/2024/scaling-monosemanticity/index.html)
    - They use [[Sparse Autoencoders]] to extract monosemantic activations from the polysemantic activations of an LLM.
### Datasets

- TruthfulQA
- ETHICS -Utilitarism
- MACHIAVELLI
- StereoSet
- CommonSenseQA

## Theory

[Katharoupolos](https://arxiv.org/pdf/2006.16236) has experimented in the past with a linearized version of the transformer architecture in which the self-attention mechanism is substituted with a linearized version, thanks to which the transformer can be run in a fast recurrent mode at inference. In particular the Softmax operation on a single value _x_ is defined as:

$$\verb|Attention|(x_{n+1} | x_0,..., x_n) = \frac{\sum^{n+1}\exp(q_{n+1} k_i^T)v_i}{\sum^{n+1}\exp(q_{n+1} k_i^T)}$$

They argue that by substituting the **exp** kernel operator with a generic linear kernel $K(x,y)= \phi(x)\phi(y)$﻿.
$$\verb|LinearAttention|(x_{n+1}) = \frac{\sum^{n+1}\phi(q_{n+1}) \phi(k_i^T)v_i}{\sum^{n+1}\phi(q_{n+1}) \phi(k_i^T)} = \frac{\phi(q_{n+1})\sum^{n+1} \phi(k_i^T)v_i}{\phi(q_{n+1})\sum^{n+1} \phi(k_i^T)}$$

Now if we consider $\phi(x) = x$﻿ (the identity function), we see that the output of this operation depends completely on this, which we’ll call `state` from now on:

$\verb|state|_n = \frac{\sum^n k_i^T v_i}{\sum^n k_i} \in \mathbb{R}^d$

Also note that this can be computed recurrently by updating the system:

$$
\begin{cases} \verb|state_sum|_{n+1} = k_{n+1}^Tv_{n+1} + \verb|state_sum|_{n} \\ \verb|state_den|_{n+1} = k_{n+1}^T + \verb|state_den|_{n} \\ \verb|LinAttn|(x_{n+1}) = \frac{q_{n+1} \cdot \verb|state_sum|_{n+1}}{q_{n+1} \cdot\verb|state_den|_{n+1}} \end{cases}
$$

As such we have proven that in Linear Attention the entire computation is based **only** on the current state, and thus all the information about the past is completely encoded within it. Per information theory then this dense state must encode all the information acquired during runtime, i.e. the **dynamic memory**.

### What is Dynamic Memory?

So, since the next token prediction depends wholly on the current `state` , I imagine it must contain all the **dynamic** information about the current generation method. For example if some information was acquired within the context window that information must be somehow encoded in this state, rather than in the model parameters (which contains **static** information).

![[image.png]]

Static knowledge is embedded in the model parameters.

![[image 1.png]]

Where is the dynamic knowledge (like the one acquired at runtime) stored?

## KV Cache
Storing previous KV pairs would be required in order for this method to work. But, this wouldn't be a problem as it's already common to use a KV cache for inference in production environments. 

Furthermore, if it's the case that the KV cache is more sophisticated, for example it has some hierarchiacal structure or it's pruned based on some importance scores, we could analyze these layered KV cache directly. 

Or maybe we could store the aggregated values of their attention scores. And then use it as a proxy of importance.


## Possible Issues

1. This discourse is valid for linear attention, while the transformers we’ll analyze have standard Attention. hopefully this will work the same as we just need to represent the information stored in the Key-Values pairs.
    1. _optionally:_ find $\phi(\cdot)$﻿ such that it better mimics the original $e^{xy}$﻿
2. It could be the case that this representation is not more representative or better than studying the pattern of activations: The pattern of activations is what has been typically studied in all these years, but this property is length dependent _(d x H x L)_ because you need to consider all the tokens of a phrase.
3. One model usually has _H_ heads of dimension _d_ and _D_ layers. Which should we use when performing this analysis?
    1. use all the heads from a middle layer (_d x H) (Anthropic)_
    2. use all the heads from an upper layer _(d x H)_
    3. use all the heads from k=3 upper-middle layers (_d x k x H) Gottesman et al._
    4. use all of them combined _(d x H x D) Zou et al._
    5. should we use the activations only? I guess not, because the query that is multiplied to this state is a linear operator (dot product), thus I expect that we need the whole of it.