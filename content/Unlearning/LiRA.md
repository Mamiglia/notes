---
title: LiRa
description: 
date: 09/12/2024
tags:
  - paper
url: https://arxiv.org/pdf/2112.03570
---
The paper proposes a novel methodology and evaluation method for Membership Inference Attack.

## Evaluation
Previously MIA were evaluated by their balanced accuracy score (informedness). The paper argues that this is wrong as this metric is symmetric and average-case metric.

> For example, when a membership inference attack is used in a training data extraction attack, false negatives are benign (some data will not be successfully extracted) whereas false positives directly reduce the utility of the attack

> Consider comparing two attacks. Attack A perfectly targets a known subset of 0.1%of users, but succeeds with a random 50% chance on the rest. Attack B succeeds with 50.05% probability on any given user. On average, these two attacks have the same attack success rate (and thus the same balanced accuracy). However, the second attack is practically useless, while the first attack is exceptionally potent.

Thus they argue that in order to correctly evaluate the MIA one should always consider the power of the method in assessing whether a specific sample is a training sample (positive) with a high degree of confidence.
For this reason they propose a different evaluation metric in which they evaluate the TPR/FPR tradeoff when FPR is low, thus they use a logarithmic [ROC curve](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) which magnifies behaviour in low FPR regime (high confidence). 

In order to have a puntual metric they fix some values of FPR (e.g. FPR=0.01%) and check the value of TPR at that threshold. This can be easily interpreted as:
- **FPR = k%**: this is the probability of misclassifying a non-member sample. When FPR is low it means that only a small part of the negatives is misclassified, thus the model is confident in the positive predictions it makes
- **TPR**: this is the probability of correctly classifying a member sample. 
- **TPR@FPR=k%**: means the probability of correct member classification when the model is (1-k)% confident, i.e. the number of correct positive predictions the model is highly  confident in.

## Method
The paper also propose a method for performing MIA named **Likelihood Ratio Attack (LiRA)**. The method works as such:
- fit a gaussian distribution on the features of the members
- fit a gaussian distribution on the features of non-members
- compute the likelihood ratio.

I don't really get why this should be more effective than SVC or LogisticRegression since this looks like Naive Bayes.

As features they use a "logit scaling" of the loss