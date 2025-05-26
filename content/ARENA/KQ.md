---
title: Untitled
description: 
date: 26/05/2025
tags:
  - unprocessed
url:
---
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

### After David 
Connect an SAE to an attention head to discover which features are looking for which others. Do so by some chunky matmul. For example the the top-k features attended by feature $i$:
$$
f_j = topk_j ||(f_i W_D) W^{QK} [W_D]_j||_2
$$
For example we may observe that feature "cat" attends to "dog".

**Roadbloacks**:
- still assuming that SAE is already encoding all the features we're interested in
- I don't feel like we're able to recover not even one of the heads of the IOI
- What happens to ignored features? I expect that the majority of features is completely ignored by the QK matrix. maybe their norm get shrunk significantly? 

Additionally we may also look into:
- Which features get ignored (by looking at the norm)
- Which tokens attend to which features
- Which features attend to which tokens

## Discoveries
- Some SAEs do LayerNorm before processing! Can I use this to make everything linear?
	- `gpt2-small-resid-post-v5-32k`
	- `gpt2-small-resid-post-v5-128k`
- What should be the distribution of feature_norm? Currently using 1
- For now I'm using KQ rather than QK
- Head 0.1 attends to same tokens. Behaviour transfers correctly to features. i.e. feature $i$ attends to itself 
- As of now I notice that decoding simplified works better than full decoding
- 