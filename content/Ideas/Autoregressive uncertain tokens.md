---
title: Autoregressive uncertain tokens
description: 
date: 11/06/2024
tags:
  - idea
status: active/draft
url:
---
When running a decoder in a autoregressive way: rather than giving in input the highest predicted token, give a weighted average of the most likely tokens.

In the following pass predict the new token together with the old (uncertain) ones. Repeat predicting the last $k=15?$ tokens. Though note that this needs $k$ times computational power.

Hopefully the result is that uncertain tokens are defined gradually with each pass, and the model has the possibility to correct themselves in latter forward passes.