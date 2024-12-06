---
title: Membership Inference Attack
description: 
date: 06/12/2024
tags:
  - summary
url:
---
The **MIA** is a technique used to infer whether a specific sample was used or not in the training process of a model. Every MIA technique works by extracting some features about the **model behaviour** from a forward pass where the input is the candidate sample. Then we can fit a classifier to try to infer whether the sample was a member of the training set.

For example the input features of a MIA could be:
- **Logits**: 
- **Entropy**: 
- **Confidence**: 
- **Loss**: 
And the classifier could be any ML model, but I've seen that most of the people use very simple models like Logistic Regression, SVC, or other classical ML techniques. 

The MIA is not a simple task, and its success rate depends both from the model architecture that one is using, from the difference of the distribution between the training and test set and finally from the training method, and in particular from the level of **overfitting** of the model.

In general it can be said that the more the model has memorized the training set (*overfitting*) the more the model is susceptible to MIA.

### Theoretical Best
As I understand it (although this is not mentioned elsewhere) what we actually search through the MIA is whether some behaviour of the model could let us infer the membership of the samples. As such what we are searching is actually a measure of the information that the features extracted $X$ give us on the $Y \sim Bern(p)$ membership, as $X \stackrel{\text{I}}{\rightarrow} Y$. 

The theoretical maximum in this case is the *Bayes Error Rate*, which is the irreducible error of a perfect classifier. Though this is impossible to compute, we can approximate it with:
- [Mutual Information](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mutual_info_score.html)
- Balanced Accuracy
- [Informedness (Youden's J)](https://en.wikipedia.org/wiki/Youden%27s_J_statistic): rescaled balanced accuracy

In order to have the most private model these metrics should be as low as possible, i.e. the information that $X$ gives us over $Y$ should be as low as possible.

### Shadow Models
Since we often don't have access to the model parameters and/or original training set, you typically train a number of similar models on similar datasets, and train your **MIA** over these shadow models. Then you apply it over the features extracted from the original model. 

### MIA for Machine Unlearning
When you apply MIA to Machine Unlearning the setting is a bit difficult. In this case you want to verify whether your model forgot about some specific samples (*forget set*). In this case you train the MIA to distinguish the model behaviour between *validation set* and *retain set*, then to test the performance of your unlearning you hope that as much as possible of your *forget set* get classified as non-member. 

