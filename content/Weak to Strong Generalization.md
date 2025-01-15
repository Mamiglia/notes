---
title: Weak to Strong Generalization
description: 
date: 15/01/2025
tags:
  - paper
url: https://arxiv.org/abs/2312.09390
---
Teachers and professors seem to be able to train students that end up being much smarter than them. For example, the mathematician Godfrey Hardy mentored Srinivasa Ramanujan, but it is generally accepted that Ramanujan was the more impressive of the two. The idea is that if less capable mentors can guide students smarter than them, maybe humans could guide superhuman AI systems.

Weak to strong generalisation is an empirical approach to learn how larger models generalise when trained by feedback from weaker supervisors. These experiments might help us predict how superhuman models might generalise when trained by feedback from humans, as well as how even more advanced models might generalise from superhuman models, and so on.

An example experiment might therefore have:

- **A weaker “aligned” supervisor**. For example, we could train GPT-2 to try to answer true/false questions accurately.
- **A stronger unaligned student**. For example, a base model the size of GPT-4 that has been pre-trained on lots of internet text (but it doesn’t know it should be answering questions accurately).

We could then use GPT-2’s predictions on true/false questions it hasn’t seen before to fine-tune the GPT-4 base model.

Because GPT-2 is a much weaker model, it’ll get a lot of the questions wrong, so you might expect the fine-tuned GPT-4 model to do at most as well as GPT-2. However, the fine-tuned model actually does a lot better than GPT-2. This suggests it is generalising well i.e. appears to learn the task ‘answer questions correctly’, rather than the task ‘answer questions like GPT-2’. This is likely happening because the GPT-4 base model has already been pre-trained on far more internet text so inherently ‘knows’ how to answer questions correctly - so GPT-2 is not teaching it new capabilities but instead is **eliciting** existing ones.

Empirical research in this area might also explore techniques for improving this generalisation. There are a number of things we might try, for example:

- **Bootstrapping** using progressively larger models. For example, rather than training a model that is twice the size straight away, we could train a model that is 33% larger, then have that train a model that is 66% larger, then 100% larger.
- **Auxiliary confidence loss**, an additional term added to the loss function which makes the larger model more confident in its decisions over time. This can help it avoid making the same mistakes as the weaker supervisors, where it is confident in its own judgement.