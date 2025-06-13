---
title: A deep dive in Attention Head 1.5 of GPT2
description: 
date: 03/06/2025
tags:
 - post
url:
---
GPT-2 attention head 1.5 presents a particular attention pattern, as it appears to attend to semantically similar pairs. For example the token *"cat"* attends to *"dog"* and *"elephant"* while the token *"red"* attends to *"green"* and *"blue"*. Most interestingly the head avoids attending to the current token, so that *"dog"* doesn't attend to itself, or other occurrences of the same token. This short research project aims to mechanistically explain such a peculiar behaviour, in the hope of developing some interesting technique to analyze attention patterns.

I start by analyzing attention pattern on common sentences and empirically observe the following behaviour:
	- A given token belonging to a specific semantic category attends to other tokens in the sequence that belong to the same category.
	- The token does **not** attend to itself, or to previous instances of itself within the sequence.
	- If no other token belonging to the same semantic category can be found in the sequence than the token attends to the *< bos >* token.

## Component ablation

### Experiment setup
In order to find out which components matter to the attention head I:
1. Engineer a specific prompt to elicit its behaviour
2. Define a metric to measure distance from expected behaviour
3. Ablate single components

In order to elicit the specific semantic behaviour of the attention head I engineered the prompt as a bunch of tokens belonging to handpicked semantic categories shuffled together. Example:
> `<bos> cat horse blue sun apple 32 red snow sun rain happy 32 blue sun green`

Then given this prompt and the empirically observed behaviour i define a mask of the empirical attention pattern: (the attention pattern i expect to see)
![[empirically_observed_attn_pattern.png]]


### Metric
Then i define a metric to measure the distance to this expected behaviour as the KL-divergence from the expected distribution:
$$
KL(\{P_Q, 1 - P_Q\} || \{1,0\}) \,\, \,\,\,\, P_Q = M \odot A
$$
Where $A$ is the attention matrix, $M$ is the empirical attention pattern. Thus i'm measuring how much probability mass is concentrated where i expect to find it. Props to David Quarel for this derivation. Concretely this develops as:
$$
\mathcal{L} = \frac{1}{|Q|} \sum_q -\log \left( \sum_k(M \odot A)_{qk} \right)
$$
i validate that this score is low only for the 5th head:
![[heads_score.png]]

### Results
Finally i proceed to mean-ablate all previous components to check which are able to retain the original score for the head 1.5. 
i find out that all previous components are irrelevant, except for:
- The embedding matrix $W_E$
- The first MLP layer
![[component_importance.png]]

Interestingly also the positional embedding seems irrelevant, as such i deduce that the attention head 1.5 is not using positional information to exclude itself or previous instances of itself from the attention pattern.

### Outcome
Knowing this, i ablate in the rest of the analysis i ablate all unnecessary components, and compute the input of the attention head 1.5 as:
$$
E = \texttt{MLP}_0(W_E) \in \mathbb{R}^{|V| \times d}
$$
Where $|V|$ is the vocabulary size and $d=768$ is the size of the vectors in the residual stream.

Using $E$ i can then compute the whole token-to-token attention scores as:
$$
A_\text{tokens} = E\,\, W_Q W_K^T \,\,E^T
$$
And when i can check that for selected semantic groups it behaves as expected, high similarity in the block diagonal and lower on the diagonal (itself):

![[ARENA/token2token_attn.png]]

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
Using the computed token-to-token attention map i can use these scores to find clusters within the tokens. I spent around ~1h finding the right hyperparameters for the [Leiden community detection algorithm](https://en.wikipedia.org/wiki/Leiden_algorithm), and I'm quite satisfied with the quality of the clusters found. Many are easily interpretable.
If you're interested in getting to know how head 1.5 clusters the tokens (world model) and "perceives the world" you can check out the visualization at this link https://mamiglia.github.io/feature-attn/.

## Mechanistic Understanding
In this paragraph i try to understand how the head 1.5 implements the aforementioned attention mechanism: semantic similarity with self-suppression. In order to think about this problem first remember that the attention maps are computed as:
$$
A(X) = X\,\, W_{QK} \,\,X^T; \,\, \text{where } W_{QK} = W_Q W_K^T \in \mathbb{R}^{L\times L}
$$
Thus it is essentially a bilinear transformation, that given a pair of tokens outputs a score. Which kind of bilinear transformation matches the described behaviour? 

### Section name
Formally, given an embedding vector $x, y, z$ such that $x \approx y$ are cosine similar and $x \negapprox z$ and $y \negapprox z$ i want:
$$x W y^T > x W x^T > x W z^T$$

To check how this looks in the observed attention pattern i plot the average attention score over the cosine similarity for each $x,y$ pair of the tokens. i empirically validate that on average the attention score is higher for similar tokens $y$ than it is for the query token $x$, and also that for dissimilar values the the attention score is lower:
![[attn_over_similarity.png]]

### Decomposition
Now to analyze and understand what's causing this peculiar behaviour let's decompose  $W = W_{sym} + W_{skew}$  as in:
- $W_{sym} = 0.5 (W + W^T)$ as symmetric matrix such that $\forall a,b: a W b^T = b W a^T$
- $W_{skew} = 0.5 (W - W^T)$ as antisymmetric matrix such that $\forall a: aWa^T = 0$

Now i can check what happens when rather than using the full $W_{QK}$ matrix i only use the symmetric or skewed one:
![[base_sym_skew_attn.png]] 
i observe that the skewed matrix not only has a much lower impact on the attention pattern over all, but it's also irrelevant to the self-suppression of the current token, as the symmetric matrix alone can still attend to correlated blocks and **also** suppress the diagonal. 

The role of $W_{skew}$ is not completely clear at this point, but evidence shows that its role is minor, or at least not interesting for our purposes. 
Evidence:
- Ablation study: we select a number of samples from the WikiText dataset that contain references to semantic groups and observe the drop in the logit space of the correct token. Ablating the entire head results in an avg. drop of 0.4393, ablating only $W_{sym}$ results in a drop of 0.049, while ablating only $W_{skew}$ results in a drop of -0.3520 (so it improves!)
- The norm of the skew-symmetric attention scores is significantly lower than the norm of the sym
- The maximum singular value of skew is somewhat lower than w_sym (1.3473 < 1.8611)
So from now on we ignore W_skew. 

In order to understand how $W_{sym}$ is able to implement such a behaviour we start by decomposing it in its eigenvalues, $W_{sym} = Q \Lambda Q^T$ which are always real since $W_{sym}$ is symmetric. Now consider that $xW_{sym} y^T = \sum_i \lambda_i (q_i^T x)(q_i^T y)$, for $x = y$ we know this means $xW_{sym} x^T = \sum_i \lambda_i (q_i^T x)^2$, which means that that if all eigenvalues were positive than the score would always be positive, as a larger projection onto eigenvectors with larger positive eigenvalues would yield a higher score.
We posit that **Self-Suppression occurs when Wsym​ has negative eigenvalues.** If x has significant components along eigenvectors qk​ for which the corresponding eigenvalues λk​ are negative, these terms λk​(qkT​x)2 will be negative and will reduce the total sum.

So, Wsym​ exhibits "self-suppression" for a vector x if x significantly aligns with directions (eigenvectors) in Wsym​ that have negative eigenvalues. This pulls the value of xWsym​xT down from what it might have been if all eigenvalues were positive or if x only aligned with positive-eigenvalue eigenvectors.

### Empirical validation

We compute the 64 eigen values of W_sym and not that 33 out of 64 total eigenvalues are negative. 

Can we now steer this model? If we scale all the negative eigenvalues by a factor $\alpha$ we can successfully steer the attention map and force it to start paying attention to the current token. 
![[steered_eigenvalues_attn.png]]
We do this by:
```pseudocode
# Compute eigenvalues (S) and eigenvectors (Q) of matrix W_QK
S, Q = eigen_decomposition(W_sym)

# Keep only the first 64 eigenvalues (assumes W_QK has rank 64)
S = S[:64]

# Zero out negative eigenvalues in S
for i in range(len(S)):
    if S[i] < 0:
	    S[i] *= scale

# Reconstruct approximate attention matrix using truncated SVD components
attn_map = E * (Q * diag(S) * Q.T)* transpose(E)
```
