---
title: Capstone Ideas
description: 
date: 23/05/2025
tags:
  - idea
url:
---
## Uninfying KQ projection
Since 
$$
K = W_k x \; \; \; Q=W_qx  \; \; \; A = \phi(K^TQ) = \phi(x^T W_k^TW_q x)
$$
Then $M = W_k^T W_q$ can be seen as the matrix matching the behaviour of the attention pattern. For example:
- if $M \approx I_d$ then it means that the attention is "looking for itself"
- if $M \approx I_r; r<d$ than it means that attention is "looking for partially same semantic tokens" , i.e. tokens which share some semantic meaning
- if $M \neq I$ than it's projecting a part of the source subspace into a different subspace of the destination. What information can we get in this case? 


### Base Idea:
recycle an SAE trained on $x$ to also check out what's happening with $q = M x = W_k^TW_q x$, basically what is the model querying for.

**Roadblocks**:
- LayerNorm actually screws things up
- I don't like to rely on SAE to interpret as they might not capture the features which are interesting to $A$


## 