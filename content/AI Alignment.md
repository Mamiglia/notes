---
title: AI Alignment
description: 
date: 13/01/2025
tags:
  - notes
url:
---
**Alignment:** making AI systems try to do what their creators intend them to do (some people call this _intent alignment_).
**Capabilities:** developing AI systems that effectively carry out the tasks that they are trying to achieve.

These two properties are often in contrast and a competitive AI system needs to show both of them in order to be properly useful. It’s also often very difficult to distinguish competence and alignment failures: it can be unclear what AI models are actually capable of, and whether failures represent:
- inability to do tasks correctly (competence); or
- that the model can do them but is trying to do something else (alignment)

## Outer VS Inner Misalignment
![aa](https://bluedot.org/u/2024/03/inner-outer-alignment-person-diagram-2.png)

Misalignment problems is usually broke down in two subtasks:
- **Outer Misalignment** refers to the complexity of expressing an ideal goal into a defined set of precise rules.
- **Inner Misalignment** refers instead to the gaming that an AI system could apply to a given proxy goal distill an internal goal.

## RLHF
RLHF is a very useful technique that allows us to align models with human behaviours.

### Method
1. You collect ideal prompt-answer pairs and finetune the model on this.
2. For every prompt you generate multiple answers and have human annotators rank them 
3. You train a Reward model based on this.
4. You finetune the original model using a loss combination of the reward model + a term to keep the original next token prediction capabilities.

### Issues
- **Sicophancy**: the tendency to elicit approval from humans in order to maximise reward, i.e. creating responses that please us rather than being really useful
- **Situaltional Awareness**: The model might understand its situation as an AI model created by a company - this might actually be useful and increase the performance of the model, but might also mean that the model modifies its behaviour based on training and deployment.
- **Scheming**: A deceptive misaligned AI. Unlike the sycophant, the schemer is not merely interested in maximising reward for its own sake – rather it harbours secret goals of its own, and is incentivised to provide helpful responses to avoid arousing the suspicion of humans until it is confident that it can execute its plans successfully. As mentioned above, this may occur if a situationally-aware AI performs according to human preferences during training, but proceeds to pursue its own (misaligned) goals when deployed.
- **Human feedback has issues**: The human annotators are not perfect and they might pass biases and issues of the annotators onto the model. Also as the models become more complex the outputs of the model might become too complicated to evaluate.
- **The model is still susceptible to attacks**: The model can still be jailbroken, and open-weights models conditioning can be removed with just a little parameter editing.

## Scalable Oversight

### [Weak to Strong generalization](https://cdn.openai.com/papers/weak-to-strong-generalization.pdf)
Weak to strong generalisation is an empirical approach to learn how larger models generalise when trained by feedback from weaker supervisors. These experiments might help us predict how superhuman models might generalise when trained by feedback from humans, as well as how even more advanced models might generalise from superhuman models, and so on.



