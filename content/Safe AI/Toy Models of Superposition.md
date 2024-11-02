---
title: Toy Models of Superposition
tags:
  - paper
url: https://transformer-circuits.pub/2022/toy_model/index.html
---
An “ideal” NN model would always map specific neuron activations to specific features of the input. For example the ideal ImageNet would have a specific neuron firing for each cat ear.

We call this phenomenon as **monosemanticity** but we note that it quite rare to observe in any deep neural network. Rather neurons often have a **superposition** of feature activations (**polisemantic**). It’s very common for models to develop such a structure as the authors argue that **superposition** is needed in order to represent more features than the model has dimensions, allowing to compress information into a small set of neurons.

operations in superposition

emulation of sparse networks

### What’s a feature?

They provide 3 different definitions, but finally argue that the one they use is:

Properties of the input which a sufficiently large neural network will reliably dedicate a neuron to representing.

e.g. curve detectors are reliably found in each and every vision model, sentiment detectors are found in any llm, and induction heads are in any sequence model (this kinda reminds me of platonic representation conjecture).

They also argue that features are usually represented by directions, for example the famous example:

V(King) - V(man) + V(woman) = V(queen)

might indicate that “royalty” and “gender” represent two distinct features as directions in the vector space.

**Privileged Basis:** Are a specific direction in a vector space which features tend to align to, thanks to specific operations that encourage this alignment (activations, convolutions). _I believe that they’re talking about the canonical basis, but i’m not sure._

## Superposition

Even when there is a privileged basis, it's often the case that neurons are "polysemantic", responding to several unrelated features. One explanation for this is the _[superposition hypothesis](https://distill.pub/2020/circuits/zoom-in/#claim-2-superposition)_. Roughly, the idea of superposition is that neural networks "want to represent more features than they have neurons", so they exploit a property of high-dimensional spaces to simulate a model with many more neurons.  
  
**Experiments:** they train a small autoencoder to retrieve sparse features from a synthetic dataset. They use a different Sparisity levels for each feature, as they argue that a model is capable of superposition only when the features are sparse, i.e. when they don’t appear often. Finally they assign a random importance score to each of the features and work with that,

![[Untitled 2.png|Untitled 2.png]]

They show that as sparsity becomes big enough the model starts representing all the features in a compressed plane. Also the feature vectors become increasingly more similar to each other.

### Phase Change

They also play around with a 2 dimensional model, trying to understand when a model decides to:

1. Store a feature linearly
2. Store a feature in superposition
3. Don’t store a feature

![[Untitled 1 2.png|Untitled 1 2.png]]

As expected sparsity is necessary to superposition occurrence, note that in the red area (superposition) the model loses the ability of representing both features at the same time, i.e. the model can succesfully retrieve cases in which x = [1 0] and cases of x = [0 1],but is unable to guess x = [1 1]. This is intuitive as we have this behaviour only for high sparsity, thus when [1 1] is unlikely to happen.

**Geometry** they explore the geometry of the features discover that the model tends to learn **antipodes** (2 features in 1 dimension) i.e. couples of features packed together. They also see that when they increase the saparsity the model starts assigning features to regular polytopes, i.e. regular structures such as triangles (3 features reprented in 2 dimensions), pentagons (5 in 2), or even square antiprism (8 in 3). These are solutions to the Thomson problem (i don’t know what it is).

Unfortunately this is only true in uniform settings (all the features are indep. and have the same sparsity and importance). But still they hypot. that in non-uniform settings you get squished polytopes.

![[Untitled 2 2.png|Untitled 2 2.png]]

**Correlation:** they show that correlated features tend to be on orthogonal vectors if possible, otherwise they are positively correlating, otherwise they are collapsed in their main principal component.

**Learning Dyanmics:** they show that while learning the model has some issues while trying to change its geometry from one structure to another. In fact the loss has some sorts of “energy jumps” between one geometry and the other. Also they show the trajectory of evolution, showing that the model first learns a PCA-like detector, then transforms it into some other regular polytope.

**Operations in Superposition:** they show that the toy model is capable of performing operations with features in superposition, and they argue that this capability is very powerful, also maybe related to grokking.

### Strategic Picture

At the end of the paper the authors draw some key elements of “solving superposition”

- Describe Activations in terms of pure features: say “dog face” = “snout” + “floppy ear”+ “fur”
- Decomposing activation space: for mechanistic interpretability we need feature disentaglement.
- Understanding Weights: transfomer circuits can be understood only if we know the activation meaning in terms of features
- Any approach to interpretability is perilous without superposition: e.g. if you want to measure hallucinations by taking cosine similarity to known hallucinatory states, you cannot do this with superposition as there might be positive interference among feature activations.