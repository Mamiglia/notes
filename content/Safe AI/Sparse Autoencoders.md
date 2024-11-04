---
title: Sparse Autoencoders
description: 
date: 11/04/2024
tags:
  - article
url: https://transformer-circuits.pub/2024/june-update/index.html#topk-gated-comparison
---
A Sparse AutoEncoder (SAEs) is a simple two layer network of the form:
$$
\begin{cases}
z = \texttt{ReLU}(W_e x) \\
\hat{x} = W_d z
\end{cases},
x \in R^d, z\in R^h, d \ll h
$$
We then optimize it with a reconstruction loss and a $L_0$ penalty (number of active components):
$$
\text{argmin}_W \sum_i ||x_i - \hat{x}_i||_2 + \lambda ||z_i||_0
$$
There are a few variation on this standard architecture, that try to achieve: 
- Reduced loss:
	- increased sparsity $L_0$
	- improved MSE
- More interpretable features

### Standard
Normally since the $L_0$ penalty is not differentiable, it's common to use the $L_1$ instead, which is known to induce sparsity.

One problem with this is that $L_1$ is a bit aggressive, and tends to **shrink** every activation.
### [TanH](https://transformer-circuits.pub/2024/feb-update/index.html#dict-learning-tanh)
They substitute the penalization term with $\tanh(z)$ as it provides the same gradient activation for low activating features, and instead it saturates and provides 0 gradient for high activation features. 
They successfully obtain better MSE and $L_0$ loss but when analyzing the features they find that they are less interpretable. 
The only explanation they can provide is that the Tanh induces higher frequency components, with very high activation patterns.

### [Training Strategy](https://www.lesswrong.com/posts/3JuSjTZyMzaSeTxKk/fixing-feature-suppression-in-saes-2#How_can_we_fix_feature_suppression_in_trained_SAEs_)
