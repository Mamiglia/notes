---
title: A deep dive in Attention Head 1.5 of GPT2
description: 
date: 03/06/2025
tags:
 - post
url:
---
GPT-2 attention head 1.5 presents a particular attention pattern, as it appears to attend to semantically similar pairs. For example the token *"cat"* attends to *"dog"* and *"elephant"* while the token *"red"* attends to *"green"* and *"blue"*. Most interestingly the head avoids attending to the current token, so that *"dog"* doesn't attend to itself, or other occurrences of the same token. This short research project aims to mechanistically explain such a peculiar behaviour, in the hope of developing some interesting technique to analyze attention patterns.

We start by analyzing attention pattern on common sentences and empirically observe the following behaviour:
- A given token belonging to a specific semantic category attends to other tokens in the sequence that belong to the same category.
- The token does **not** attend to itself, or to previous instances of itself within the sequence.
- If no other token belonging to the same semantic category can be found in the sequence than the token attends to the *< bos >* token.

## Component ablation
In order to find out which components matter to the attention head I:
1. Engineer a specific prompt to elicit its behaviour
2. Define a metric to measure distance from expected behaviour
3. Ablate single components

In order to elicit the specific semantic behaviour of the attention head we engineer the prompt as a bunch of tokens belonging to handpicked semantic categories shuffled together. Example:
> `<bos> cat horse blue sun apple 32 red snow sun rain happy 32 blue sun green`

Then given this prompt and the empirically observed behaviour we define a mask of the empirical attention pattern:
![[expected_mask.png]]

Then we define a metric to measure the distance to this expected behaviour as the KL-divergence from the expected distribution:
$$
KL(\{P_Q, 1 - P_Q\} || \{1,0\}) \,\,\, P_Q = M \odot A
$$
Where $A$ is the attention matrix, $M$ is the empirical attention pattern. Thus we're measuring how much probability mass is concentrated where we expect to find it. Props to David Quarel for this derivation. Concretely this develops as:
$$
\mathcal{L} = \frac{1}{|Q|} \sum_q -\log \left( \sum_k(M \odot A)_{qk} \right)
$$
We validate that this score is low only for the 5th head:
![[heads_score.png]]

Finally we proceed to mean-ablate all previous components to check which are able to retain the original score for the head 1.5. 
We find out that all previous components are irrelevant, except for:
- The embedding matrix $W_E$
- The first MLP layer
![[component_importance.png]]
Interestingly also the positional embedding seems irrelevant, as such we deduce that the attention head 1.5 is not using positional information to exclude itself or previous instances of itself from the attention pattern.

Knowing this, we ablate in the rest of the analysis we ablate all unnecessary components, and compute the input of the attention head 1.5 as:
$$
E = \texttt{MLP}_0(W_E) \in \mathbb{R}^{|V| \times d}
$$
Where $|V|$ is the vocabulary size and $d=768$ is the size of the vectors in the residual stream.

Using $E$ we can then compute the whole token-to-token attention scores as:
$$
A_\text{tokens} = E\,\, W_Q W_K^T \,\,E^T
$$
And when we can check that for selected semantic groups it behaves as expected, high similarity in the block diagonal and lower on the diagonal (itself):

![[token2token_attn.png]]

```
                                             Most Attended
┏━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃ Input Token ┃ Top Attended Tokens                                                                               ┃
┡━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┩
│ ' red'      │  Green,  Blue,  green,  blue, green,  Yellow,  GREEN, blue, Green,  White                         │
│ ' 69'       │  72,  82,  70,  62,  71,  80,  68,  67,  66,  78                                                  │
│ 'Monday'    │ Wednesday, Tuesday,  Wednesday,  Tuesday, Friday,  Tonight,  Friday,  tonight, Tonight,  tomorrow │
│ ' John'     │  They,  Their, their,  THEIR, They,  their,  His, Their,  THEY,  they                             │
│ ' +'        │  -,  -=,  ±, =-,  =,  –, =/,  --, =,  —                                                           │
│ ' if'       │  when,  after,  When,  After,  if,  for, After,  where, When,  despite                            │
│ ' Italy'    │  Iceland,  Turkish,  Pakistani,  Auckland,  Portugal,  Guatemala,  Zealand,  Pakistan,  Mexican,  │
│             │ Chile                                                                                             │
└─────────────┴───────────────────────────────────────────────────────────────────────────────────────────────────┘
```

## Clustering
Using the computed token-to-token attention map we can use these scores to find clusters within the tokens. I spent around ~1h finding the right hyperparameters for the [Leiden community detection algorithm](https://en.wikipedia.org/wiki/Leiden_algorithm), and I'm quite satisfied with the quality of the clusters found. Many are easily interpretable.
If you're interested in getting to know how head 1.5 clusters the tokens (world model) and "perceives the world" you can check out the visualization at this link (INSERT LINK).

## Mechanistic Understanding
In this paragraph we try to understand how the head 1.5 implements the aforementioned attention mechanism: semantic similarity with self-suppression. In order to think about this problem first remember that the attention maps are computed as:
$$
A(X) = X\,\, W_{QK} \,\,X^T; \,\, \text{where } W_{QK} = W_Q W_K^T
$$
Thus it is essentially a bilinear transformation, that given a pair of tokens outputs a score. Which kind of bilinear transformation matches the described behaviour? 

