
**TL;DR:** GPT-2's head 1.5 directs attention to **semantically similar** tokens and actively **suppresses self-attention**. This mechanism is driven by a symmetric bilinear form with negative eigenvalues, which enables suppression. The head computes attention purely based on token identity, **independent of position**. We **cluster** tokens semantically, interpret the weights to explain the attention scores, and **steer** self-suppression by tuning eigenvalues.

# The Self-Hating Attention Head: A Mechanistic Explanation of Head 1.5

Within GPT-2 small lies an unusual component: attention head 1.5. At a glance, it's a "semantic similarity" head. It excels at connecting related concepts: the token `cat` attends to `dog`, and `red` attends to `green` and `blue`. But here's the odd part: it actively avoids attending to itself. The token `dog` will attend to other animals in the context, but it will not attend to itself or other instances of the token `dog`.

This behavior is too specific to be an accident. This research project aims to find the mechanistic explanation for this semantic grouping and self-avoidance, hoping to develop useful techniques for analyzing attention patterns along the way.

## The Behaviour: Three Simple Rules
Before diving into the mechanistic analysis, let's establish exactly what GPT-2 head 1.5 (second layer, 6th head) is doing. After analyzing its attention patterns across hundreds of sequences, the behavior can be summarized by three remarkably consistent rules:
1. **Semantic Clustering**: A token attends to other tokens in the sequence that belong to the same semantic category. `Monday` attends to `Tuesday` and `Friday`, `him` attends to `her` and `their`;
2. **Self-Suppression**: A token does **not** attend to itself, even when it appears multiple times in the sequence. If `dog` appears at positions 3 and 7, the `dog` at position 7 will not attend to itself or to the `dog` at position 3.
3. **Fallback to Beginning**: If no other token in the sequence belongs to the same semantic category, the token attends to the `<bos>` token.

These rules hold remarkably consistently across different types of semantic categories, colors, months, days of the week, numbers, names, even verbs and logical operators.
Broadly, the behaviour of this head as a semantic head was already mentioned [in this post](https://www.alignmentforum.org/posts/xmegeW5mqiBsvoaim/we-inspected-every-head-in-gpt-2-small-using-saes-so-you-don), but there it's only described as a "*Succession or pairs related behavior*".
## Part 1: Finding What Matters
First, I needed to isolate this behavior and find the components responsible for it. This required a three-step process: design a prompt to reliably trigger the behavior, define a metric to measure it, and ablate components to see which ones break it.
### The Setup
To reliably trigger the head's behavior, I created a simple prompt by shuffling tokens from various hand-picked semantic categories. This creates a context where the head has many opportunities to demonstrate its preference for in-category attention.

> `<bos> cat horse blue sun apple 32 red snow sun rain happy 32 blue sun green`

Based on the three rules observed above, I defined an "expected" attention pattern for this prompt. For example, `cat` should attend to `horse`, but not to `cat` or `blue`. This gives me a target mask representing the idealized behavior of the head.
![[empirically_observed_attn_pattern.png]]
### The Metric
To measure how well the head's actual attention pattern, $A$, matches my expected pattern, $M$, I used a KL divergence-based score: $KL(\{P_Q, 1 - P_Q\} || \{1,0\})$ where $P_Q = M \odot A$. The goal here is to measure how much of the attention probability mass is concentrated where the mask says it should be. This leads to the following loss function (credit to David Quarel for the derivation):

$$
L = \frac{1}{|Q|} \sum_q -\log \left( \sum_k (M \odot A)_{qk} \right)
$$

A lower score means a better match. As expected, a survey of all heads in the model shows that head 1.5 is an outlier with a uniquely low score, confirming it's specialized for this task.
![[heads_score.png]]

### The Ablation
With a reliable metric, I could perform a mean-ablation study. I systematically replaced the output of each preceding component with its mean value and checked if the score for head 1.5 changed.

Surprisingly, most components are irrelevant. The head's behavior remained unaffected after ablating any component, except for:
1. The token embedding matrix, $W_E$.
2. The first MLP layer, $MLP_0$.
![[component_importance.png]]

Interestingly, ablating the positional embeddings ($W_{pos}$) had almost no effect. This is a crucial clue: the head isn't using positional information to avoid attending to itself. The mechanism must be inherent to the token representations themselves.

From this, I concluded that the essential input to head 1.5 is simply:
$$
E = MLP_0(W_E) \in \mathbb{R}^{|V| \times d}
$$

This matrix $E$ contains a "processed" embedding for every token in the vocabulary, and it's all head 1.5 needs to perform its function.

## Part 2: The World According to Head 1.5
Using this simplified input $E$, we can circumvent the rest of the network and compute a full token-to-token attention score matrix directly:

$$
A_{tokens} = E \,\,W_Q W_K^T\,\, E^T
$$

Here, $W_{QK} = W_Q W_K^T$ is the attention head's effective weight matrix. Visualizing this for selected semantic groups reveals the behavior perfectly: high scores within a semantic block (e.g., colors attending to other colors) but low scores on the diagonal (a token attending to itself).
![[token2token_attn.png]]

The head's semantic groupings are robust and intuitive:

| **Input Token** | **Top Attended Tokens**                                                                       |
| --------------- | --------------------------------------------------------------------------------------------- |
| ' red'          | Green, Blue, green, blue, green, Yellow, GREEN, blue, Green, White                            |
| ' 69'           | 72, 82, 70, 62, 71, 80, 68, 67, 66, 78                                                        |
| 'Monday'        | Wednesday, Tuesday, Wednesday, Tuesday, Friday, Tonight, Friday, tonight, Tonight, tomorrow   |
| ' Italy'        | Iceland, Turkish, Pakistani, Auckland, Portugal, Guatemala, Zealand, Pakistan, Mexican, Chile |
### Clustering
Using this attention map, I ran the Leiden community detection algorithm to cluster the entire vocabulary. The resulting clusters are surprisingly coherent and offer a fascinating glimpse into the "world model" of this specific head. You can explore this visualization yourself at [this link](https://mamiglia.github.io/feature-attn/).

## Part 3: The Mechanism of Self-Suppression
How does a single matrix, $W_{QK}$, implement this complex behavior? Formally, for a query vector $x$, a similar vector $y$ (high cosine similarity), and a dissimilar vector $z$, we want:

$$
x W_{QK} y^T > x W_{QK} x^T > x W_{QK} z^T
$$

Plotting the average attention score against cosine similarity confirms this empirically. The peak attention score is not at a similarity of 1.0 (self-attention), but slightly below it, showing the head prefers tokens that are similar, but not identical.
![[attn_over_similarity.png]]

### Decomposing the Matrix
To understand how $W_{QK}$ works, I decomposed it into its symmetric and skew-symmetric parts:
$$
\begin{cases}
W_{sym} = 0.5(W_{QK} + W_{QK}^T) \\
W_{skew} = 0.5(W_{QK} - W_{QK}^T)
\end{cases}
$$

This decomposition is useful because the skew-symmetric part always has zero contribution to self-attention ($x W_{skew} x^T = 0$).

When I tested these components separately, the result was clear. The symmetric matrix, $W_{sym}$, was able to reproduce the full behavior on its own: high off-diagonal attention within semantic blocks and low diagonal self-attention. The skew-symmetric part had a negligible effect.
![[base_sym_skew_attn.png]] 

+++ About $W_{skew}$


A wealth of evidence suggests the minor role of $W_{skew}$ in this mechanism. Firstly, the Frobenius norm of the attention scores produced by $W_{skew}$ is lower than that of $W_{sym}$, meaning its overall contribution to the final logits is small. Secondly, its maximum singular value is smaller than that of $W_{sym}$ (1.34 vs 1.86), indicating that even its strongest directional effect is weaker. Most tellingly, a downstream ablation study reveals that removing $W_{skew}$ actually *improves* model performance on a related task. When measuring the logit drop on semantically relevant tokens, ablating the full head hurts performance (a drop of 0.44), but ablating only the skew part helps performance (a negative drop of -0.35). This suggests that $W_{skew}$ might be adding a slight, counter-productive noise for this specific function. Given these converging lines of evidence, I'll assume that the core self-suppression mechanism is not driven by the skew-symmetric component, allowing us to focus our investigation entirely on $W_{sym}$.


+++
### The Role of Eigenvalues
So, the mystery is contained entirely within $W_{sym}$. How does it suppress self-attention? We can decompose it using its eigenvalues ($\lambda_i$) and eigenvectors ($q_i$): $W_{sym} = Q \Lambda Q^T$. The attention score is then:
$$
x W_{sym} x^T = \sum_i \lambda_i (q_i^T x)^2
$$
If all eigenvalues $\lambda_i$ were positive, this score would always be positive. A vector would achieve its highest score by aligning with the eigenvectors corresponding to the largest positive eigenvalues.

This leads to our central hypothesis: **Self-suppression occurs because** $W_{sym}$ **has negative eigenvalues.** If a vector $x$ has a significant projection onto an eigenvector $q_k$ whose eigenvalue $\lambda_k$ is negative, that component $\lambda_k (q_k^T x)^2$ will be negative, reducing the total score.

The head suppresses self-attention for a vector $x$ by having it align with "suppressive directions" in the space defined by $W_{sym}$.

## Part 4: Validation by Steering
This is a testable hypothesis. First, I computed the 64 eigenvalues of $W_{sym}$ and found that 33 of them are negative. This strongly supports the theory.

The ultimate test is to see if we can control the behavior by manipulating these eigenvalues. I created a steering mechanism to scale all negative eigenvalues by a factor $\alpha$.
```python
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
![[steered_eigenvalues_attn.png]]
## Conclusion
This study offers a mechanistic account of GPT-2 attention head 1.5’s unusual behavior. Its tendency to attend to semantically related tokens, while suppressing self-attention, appears to arise from a symmetric bilinear form with carefully placed negative eigenvalues. This effect seems to operate independently of position, relying only on transformed token embeddings. 
Decomposing the attention matrix and inspecting its spectrum suggests that negative eigenvalues play a key role in self-suppression. Moreover, this behavior can be steered by adjusting the spectrum, pointing to a possible causal link between spectral structure and function. 
These results add to our grasp of attention in LLMs and hopefully hint at new ways to interpret and steer their internal workings.

![vbgjj](https://imgur.com/a/LTOk544)

![Imgur](https://imgur.com/EtOw2u9)



