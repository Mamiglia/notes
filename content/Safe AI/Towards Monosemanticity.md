[https://transformer-circuits.pub/2023/monosemantic-features/index.html](https://transformer-circuits.pub/2023/monosemantic-features/index.html)

In this paper hey propose an approach to separate and interpret the polysemantic neurons of a MLP layer of a transfomer into a set of distinct features.

In particular, given the activations that come out of an MLP layer they train a 2-layer Autoencoder that reconstructs the input vector. What’s different from previous autoencoders is that this time the hidden state is considerably larger than the original vector (8x - 256x). Also they use a L1 loss on the hidden state activations to induce sparsity and reduce the number of active hidden neurons at each step.

  

They also show something interesting: even without superposition a neuron can still be polysemantic, this is because in many cases models achieve lower loss by representing multiple features ambiguously (in a polysemantic neuron) than by representing a single feature unambiguously and ignoring the others.

### Sparse Dictionary Learning

Given the input features $x_1, …, x_n, x_i \in R^d$, Learn a matrix $D \in R^{d\times n}$ such that:

$$
\sum_i||x_i - Dr_i||_2 + \lambda||r_i||_0
$$


i.e. we minimize the reconstruction loss, and ensure that the representations $r_i$ are sparse enough.

Exact Dictionary Learning is NP-Hard, while an exact approach could be used, the authors decide to use a relaxed version, which employs a simple autoencoder, as that’s also what the network is using to process the features (it cannot use NP-Hard algorithms).

### SAE Training

They observe that scale really matters and use 8B datapoints. They also use a _Neuron Resampling_ technique in which they reset the weights of dead neurons.

If you are curious get more infos here:

[https://transformer-circuits.pub/2023/monosemantic-features/index.html#appendix-autoencoder](https://transformer-circuits.pub/2023/monosemantic-features/index.html#appendix-autoencoder)

### Features

for each of the features and their explanations (context) they try to prove that:

- the feature is specific (when it activates the context is present)
- the feature is sensitive (when the context is present, the feature activates)
- the feature has some downstream behaviour
- the feature has no one-to-one correspondence with any neuron
- the feature is _universal_ (it’s found also in other LLMs)

they analyze this on simple to evaluate contexts and features, relating to: base64 strings, DNA code, hebrew and arabic scripts. These are easy as all these contexts are based on specific unicode ranges.