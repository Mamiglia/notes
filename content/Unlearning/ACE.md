---
title: ACE
description: 
date: 10/01/2025
tags:
  - paper
url: https://arxiv.org/abs/2501.01633
---
ACE explores the possibility of using T2I models to modify images of which the concepts were erased before release. E.g. using an image of pikachu, and asking to model to put sunglasses on it, while pikachu was erased from the model. This happens because [[MACE]], [[RECE]] or ESD remove the concept from the conditioned modules, while it doesn't touch the unconditional modules.

To address this ACE performs a finetuning with a number of specific losses, and modifies both conditional and unconditional components.

- **Prior-Guided Unconditional Loss:** Aligns the unconditional noise prediction with a guidance that includes both target concept erasure and random correction from prior concepts. This loss helps prevent the generation of erased concepts while maintaining the model's ability to generate non-target concepts through a balanced guidance approach.
- **Prior Constraint Loss:** Ensures the prediction of prior (non-target) concepts in the new model remains consistent with the original model's predictions. This loss helps prevent overfitting during training and maintains the model's general capabilities while erasing specific concepts.
- **ESD Loss**: Adopted from the original ESD method, this loss aligns the conditional noise prediction of target concepts with a conditional erasure guidance. The loss helps guide the model away from generating the target concept when it's explicitly requested in the input prompt.

