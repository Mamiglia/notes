---
title: Sparse Autoencoders
description: 
date: 11/04/2024
tags:
  - paper
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
They first modify the inner state with a **scale** parameter.
$$
z = s \odot ReLU(Wx)
$$
Then they test various forms of second-phase of training, in which they finetune the trained model but they freeze a part of the parameters, and remove the penalty.
![[Pasted image 20241104142818.png]]

They find out that the last three methods (in which they freeze the decoder) greatly reduce feature sparsity (which is undesirable).

They also show that the other methods (freeze the encoder) reduce the MSE, improve perplexity, and increase L2 norm ratio in respect of the input (which is a proxy of feature suppression). Overall they conclude that this training strategy can improve the reconstruction of the features, by reducing feature shrinkage.

### [Gated SAE](https://arxiv.org/pdf/2404.16014)
These have been proposed to address the issues of feature *shrinking* in standard SAEs. They first say that an SAE has two roles:
- *detects* which features are active
- *estimates* the magnitude of the active features
The L1 loss is crucially important for detecting disentangled features, but it's detrimental in the second case in which it's a source of unwanted bias toward 0.

They propose to decompose the model into two parts:

$$
\begin{align}
g(x) &= W_g x + b_g \\
m(x) &= W_m x + b_m \\
z(x) &= (g(x) > 0) \odot ReLU(m(x))
\end{align}
$$
In practice they revive Gated Linear Units for this purpose, and they separately estimate the detection and estimation parts. In practice, to avoid doubling the parameters of the encoder they tie the weights of $W_m, W_g$ such that:
$$
W_m = \exp(r_m) W_g 
$$ Such that they share the same vector direction but have different norms and biases, so at the end they have $2h$ additional parameters. The result is that in practice this is equivalent to using a jump ReLU, where the $\theta$ can be learned and is $\theta = b_m - \exp(r_m)\odot b_g$.
<img src="https://res.cloudinary.com/lesswrong-2-0/image/upload/f_auto,q_auto/v1/mirroredImages/wZqqQysfLrt2CFx4T/zzrdot3xexvcz3mqghn8" alt="jump relu" width="400"/>

Then at runtime we use the following training loss:
$$
\mathcal{L} = \mathcal{L}_\text{MSE} +\lambda||\verb|ReLU|(\pi)||_1 + ||x-\verb|dec|_\text{frozen}(ReLU(\pi))||_2^2
$$
- the first term is the **reconstruction loss**.
- The second is the penalization of the activations, which is needed to induce sparsity. Note that we cannot use the step function $(\cdot > 0)$ because it's not differentiable, so we use ReLU
- Finally the last term is an auxiliary loss that tries to reconstruct $x$ but considering only the detected features. Note that in this step we freeze the decoder to avoid passing gradient there. (why not $ReLU(\pi) \odot ReLU(m)$?)


### [TopK SAE](https://cdn.openai.com/papers/sparse-autoencoders.pdf)
TopK SAE work by zeroing out all the non top-k activations. 
$$
\hat{z} = \text{Top-k}(z)
$$
In this way only the top-k activations are kept and this enforces a specific $L_0$ sparsity on the autoencoder. During training in fact it's unnecessary to use the $L_1$ penalty, and thus they avoid the issue of activation shrinkage.

Enforcing a specific $L_0$ could be handy, as it avoids retraining different models until we find a desired sparsity, but it's suboptimal.
## [Comparison](https://transformer-circuits.pub/2024/june-update/index.html#topk-gated-comparison)
By comparing the Gated SAE with the Top-K and standard ones it turns out that the both Gated and TopK are a pareto improvement over the standard SAE. 
![[SAE-comparison.png]]When comparing different SAE methods it's important to also look at the interpretability of the features found as $L_0$ and MSE are only proxies of what we actually are looking for, interpretable features, in this case both Gated and Top-K find features that are as interpretable as in the standard SAEs.
