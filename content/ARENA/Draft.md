---
title: Untitled
description: 
date: 16/06/2025
tags:
  - unprocessed
url:
---
>*TL;DR*: gpt2-small's head L1H5 directs attention to semantically similar tokens and actively suppresses self-attention. The head computes attention purely based on token identity, independent of position. This mechanism is driven by a symmetric bilinear form with negative eigenvalues, which enables suppression. We cluster tokens semantically, interpret the weights to explain the attention scores, and steer self-suppression by tuning eigenvalues.
>
>_work performed as part of ARENA 5.0 Capstone project_



## Introduction
Within gpt2-small lies an unusual component: attention head L1H5[^1] which fires on semantically similar concepts. It excels at connecting related concepts: the token `cat` attends to `dog`, and `red` attends to `green` and `blue`. Normally, this would be unsurprising, as we would expect embedding vectors to already cluster based on topic/semantic categories. But oddly enough, for this head, tokens do not attend to themselves. For example, the token `dog` will attend to other animals in the context, but it will not attend to itself or other instances of the token `dog`.

This behavior is too specific to be an accident. This research project aims to find the mechanistic explanation for this semantic grouping and self-avoidance, with the goal to develop useful techniques for analyzing attention patterns along the way.

## The Behaviour: Three Simple Rules
Before diving into the mechanistic analysis, let's establish exactly what GPT-2 head L1H5 is doing. After analyzing its attention patterns across hundreds of sequences, the behavior can be summarized by three rules:
- **Semantic Clustering**: A token attends to other tokens in the sequence that belong to the same semantic category. Examples:
    - `cat` attends to `dog` and `horse`, (animals)
    - `red` attends to `blue`, `green` and `yellow`. (colours)
    - `Monday` attends to `Tuesday` and `Friday`. (days of the week).
- **Self-Suppression**: A token does **not** attend to itself, even when it appears multiple times in the sequence.
- **Fallback to Beginning**: If no other token in the sequence belongs to the same semantic category, the token attends to the `<bos>` (beginning of sequence) token.

These rules hold remarkably consistently across different types of semantic categories, colours, months, days of the week, numbers, names, even verbs and logical operators. Broadly, the behaviour of this head as a semantic head was already mentioned in [We Inspected Every Head In GPT-2 Small using SAEs So You Don’t Have To](https://www.lesswrong.com/posts/S99qYp2cCYJ6Yg82G/we-inspected-every-head-in-gpt-2-small-using-saes-so-you "null"), but there it's only described as a "Succession or pairs related behavior".

## Part 1: Finding What Matters
First, we need to isolate this behavior and find the components responsible for it. This required a three-step process: design a prompt to reliably trigger the behavior, define a metric to measure it, and ablate components to see which ones break it.

### The Setup
To reliably trigger the head's behavior, I created a simple prompt by shuffling tokens from various hand-picked semantic categories. This creates a context where the head has many opportunities to demonstrate its preference for in-category attention.

```
<bos> blue sad cat purple purple 24 blue cat purple sheep 69 32 happy horse angry
```
[View Attention Pattern](attention_pattern.html)

Based on the three rules observed above, I defined an "expected" attention pattern for this prompt. For example, `cat` should attend to `horse`, but not to `cat` or `blue`. This gives me a target mask representing the idealized behavior of the head.
![[expected _mask.png]]
_Example of expected attention pattern._

### Semantic Category Score
To measure how well the head's actual attention pattern, $A$, matches the expected pattern, $M$, we use a KL divergence-based score: $KL(\{1,0\} || \{P_q, 1 - P_q\})$ where $P_Q​=M\odot A$. The goal here is to measure how much of the attention probability mass is concentrated where the mask says it should be. This leads to the following loss function (credit to @David Quarel for the derivation):

$$
\mathcal{L} = \frac{1}{|Q|}\sum_q - \log{\sum_k (M \odot A)_{qk}} 
$$
A lower score means a better match. As expected, a survey of all heads in the model shows that L1H5 is an outlier with a uniquely low score, confirming it's specialized for this task.
![[surprisal.png]]

+++ Loss derivation

We define this metric as the distance from the expected behaviour described above. We encode the expected behaviour through the mask matrix M, and define Pq​=M⊙A as the amount of probability mass that the attention scores places on other tokens in the same semantic category. We can then measure the distance of Pq​,1−Pq​ as the KL divergence from the ideal distribution 1,0 where all the probability mass is concentrated where we expect it to be. Then this derivation follows

$$
\begin{align}
KL(\{1,0\} || \{P_q, 1 - P_q\}) &= 1 \ln \frac{1}{P_q} + 0 \frac{0}{1 - P_q} \\
&= -\ln P_q \quad \quad \quad \quad \quad \quad\quad \text{Shannon Information} \\
&= -\ln \left(\sum_k (M \odot A)_{qk}\right) \\

\end{align}

$$

A KL divergence between $\{1,0\}$ and $\{P_q​,1−P_q​\}$ devolves in $−\ln P_q$​. This is also called Shannon Information. We then take the average across all queries $q$.

+++

### The Ablation
With a reliable metric, I could perform a mean-ablation study. I systematically replaced the output of each preceding component with its mean value and checked if the score for L1H5 increases; if it does it means that the component is relevant for L1H5.

Surprisingly, 2 out of 4 components are completely irrelevant. The head's behaviour appears to be affected only by:
- The token embedding matrix, $W_E$​.
- The first MLP layer, $\texttt{MLP}_0$​.
- The residual stream around the first MLP.
![[component_importance.png]]

Interestingly, ablating the positional embeddings ($W_{pos}$​) and the previous attention layer ($\texttt{Attn}_0$) had almost no effect. This is a crucial clue: the head isn't using positional or sequential information to avoid attending to itself. The mechanism must be inherent to the token representations themselves, which in turn depend only on the embedding matrix ($W_E$) and the MLP.

![[component_ablation.png]]

From this, I concluded that the essential input to L1H5 can be represented simply as:
$$
E = \texttt{MLP}_0(W_E) + W_E \quad \in \mathbb{R}^{|V|\times d}
$$
This matrix E contains a "processed" embedding for every token in the vocabulary, and it's all L1H5 needs to perform its function.

## Part 2: The World According to L1H5
Using this simplified input E, we can circumvent the rest of the network and compute a full token-to-token attention score matrix directly:

$$A_{tokens}​=E\,\, W_Q ​W_K^T \,\,​ E^T$$

Here, $W_{QK}​ = W_Q ​W_K^T$​ is the attention head's QK circuit. Visualizing this for selected semantic groups reveals the behavior perfectly: high scores within a semantic block (e.g., colors attending to other colors) but low scores on the diagonal (a token attending to itself).

_Tokens attend to other semantically related tokens, but not themselves (low diagonal values)._

The head's semantic groupings are robust and intuitive:

|                 |                                                                                               |
| --------------- | --------------------------------------------------------------------------------------------- |
| **Input Token** | **Top Attended Tokens**                                                                       |
| `red`           | Green, Blue, green, blue, green, Yellow, GREEN, blue, Green, White                            |
| `69`            | 72, 82, 70, 62, 71, 80, 68, 67, 66, 78                                                        |
| `Monday`        | Wednesday, Tuesday, Wednesday, Tuesday, Friday, Tonight, Friday, tonight, Tonight, tomorrow   |
| `Italy`         | Iceland, Turkish, Pakistani, Auckland, Portugal, Guatemala, Zealand, Pakistan, Mexican, Chile |

### Clustering
Using this attention map, I ran the Leiden community detection algorithm to cluster the main 3000 tokens of English language. The resulting clusters are surprisingly coherent and offer a fascinating glimpse into the "world model" of this specific head. You can explore this interactive map for yourself [here](https://mamiglia.github.io/feature-attn) .[^2]

```embed
title: "GPT2 Head 1.5 Visualizer"
image: "https://mamiglia.github.io/favicon.ico"
description: "Filter by Cluster:"
url: "https://mamiglia.github.io/feature-attn"
```


If you do you may note some interesting clusters:
![[clusters.png]]

Note also that tokens don't usually attend to themselves (or different versions of themselves). For example `east` doesn't attend to `East`, `Eastern`, `eastern`.

## Part 3: The Mechanism of Self-Suppression
How does a single matrix, $W_{QK}$​, implement this complex behavior? Formally, for a query vector $x$, a similar vector $y$ (high cosine similarity), and a dissimilar vector $z$, we want:

$$x\,W_{QK}​\,y^T>x\,W_{QK}\,​x^T>x\,W_{QK}​\,z^T$$

To empirically verify this we can plot the average attention score obtained by a pair $x,y$ in L1H5 against their initial similarity. We can notice that the peak attention score is not at a similarity of 1, but below it, at circa 0.7, showing the head prefers tokens that are similar, but not identical.

![[attn_over_similarity.png]]
_Attention score between similar tokens. Note that most of the tokens have low similarity, so most of the mass concentrated between 0.1 and 0.7. Above that we have less data and thus much more variability._

### Decomposing the Matrix
To understand how $W_{QK}$​ works, I decomposed it into its symmetric and skew-symmetric parts:

$$
\begin{align} 
W_{sym} &​= (W_{QK}​+W_{QK}^T​)/2 \\
W_{skew​}&=(W_{QK}​−W_{QK}^T​)/2 \\
W_{QK} &= W_{sym} + W_{skew}

\end{align}​$$
This decomposition is useful because the skew-symmetric part always has zero contribution to self-attention ($x W_{skew​} x^T=0$).

When I tested these components separately, the result was clear. The symmetric matrix, $W_{sym}$​, was able to reproduce the full behavior on its own: high off-diagonal attention within semantic blocks and low diagonal self-attention. The skew-symmetric part had a negligible effect.

![[base_sym_skew_attn.png]]

+++ About $W_{skew}$​

We note that the norm of attention scores produced by $W_{skew}​$ is significantly lower than the $W_{sym}​$, and its maximum singular value is comparatively lower than the then $W_{sym}​$ (1.3473 vs. 1.8611), indicating a smaller overall contribution to the final logits.

Additionally I set up a quick ablation study in which I observe the drop in logit value of the correct token after ablating some component, in a number of selected samples from the WikiText dataset that contain references to semantic groups. While ablating $W_{sym}​$ results in a drop of 0.049, ablating $W_{skew}​$ actually **improves** performance (a drop of -0.3520), hinting it might introduce some counter-productive noise for this specific function.

While these observations might hint at $W_{skew}​$ being involved in encoding ordering or sequence information, its precise role in the self-suppression mechanism appears minor, prompting us to primarily focus on $W_{sym}$​ for further investigation.

+++
### The Role of Eigenvalues
So, the mystery is contained entirely within $W_{sym}$​, how does it suppress self-attention? We can decompose it using its eigenvalues ($\lambda_i$) and eigenvectors ($p_i$​): $W_{sym}​ = P\Lambda P^T$. The attention score is then:
$$x\,W_{sym}\,​x^T = \sum_i ​\lambda_i​(q_i^T​x)^2 $$
If all eigenvalues $\lambda_i$​ were positive, this score would always be positive. A vector would achieve its highest score by aligning with the eigenvectors corresponding to the largest positive eigenvalues.

This leads to our central hypothesis: **Self-suppression occurs because** $W_{sym}​$ **has negative eigenvalues.** If a vector $x$ has a significant projection onto an eigenvector $p_j$​ whose eigenvalue $\lambda_j$​ is negative, that component $\lambda_j ​(q_j^T ​x)^2$ will be negative, reducing the total score.

The head suppresses self-attention for a vector $x$ by having it align with "suppressive directions" in the space defined by $W_{sym}​$.

## Part 4: Validation by Steering
This is a testable hypothesis. First, I computed the 64 eigenvalues of $W_{sym}$​ and found that 33 of them are negative. This strongly supports the theory.

The ultimate test is to see if we can control the behavior by manipulating these eigenvalues. I created a steering mechanism to scale all negative eigenvalues by a factor $\alpha \in \mathbb{R}$.
```
# Decompose the symmetric matrix
eigenvalues, eigenvectors = eigen_decomposition(W_sym)

# Scale the negative eigenvalues
for i in range(len(eigenvalues)):
    if eigenvalues[i] < 0:
        eigenvalues[i] *= alpha

# Reconstruct the steered matrix
W_steered = eigenvectors * diag(eigenvalues) * eigenvectors.T

# Compute new attention map
attn_map = E * W_steered * E.T
```

As it can be seen by the plot below, when scaling alpha one can successfully steer the attention map and force it to start paying attention to the current token, while also maintaining the similarity of semantically related tokens:

## Conclusion
This study offers a mechanistic account of gpt2-small attention head L1H5’s unusual behavior. Its tendency to attend to semantically related tokens, while suppressing self-attention, appears to arise from a symmetric bilinear form with carefully placed negative eigenvalues. This effect seems to operate independently of position, relying only on transformed token embeddings. Decomposing the attention matrix and inspecting its spectrum suggests that negative eigenvalues play a key role in self-suppression. Moreover, this behavior can be steered by adjusting the spectrum, pointing to a possible causal link between spectral structure and function. These results add to our grasp of attention in LLMs and hopefully hint at new ways to interpret and steer their internal workings.

[^1]: Layer 1, Head 5

[^2]: https://mamiglia.github.io/feature-attn
