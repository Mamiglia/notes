---
title: Reliable Weak-to-strong monitoring of LLM agents
description:
date: 23/02/2026
tags:
  - idea
url:
---
This paper introduces benchmark for monitors in an adversarial fashion.  They validate their approach in an adversarial setting, each agent has a primary (benign) goal and a secondary (malign) goal. The monitor needs to catch the agent performing its secondary (malign) task. They use **SHADE-Arena and CUE-SHADE-Arena** 

They try to address 5 main research questions:

1. How agent awareness of monitors affects reliability -> Affects a lot, agents are more effective at deception if they are aware that their CoT is being monitored
2. Sophisticated malign agents can be much more effective at deceiving monitors.
3. How monitor awareness of agent goals affects reliability -> Not much. Interesingly though even when the monitor is aware **also** of the malign goal of the agent it sometimes fails to catch it (even though it knows what to look for)
4. Which scaffolding are most adversarially reliable -> summaries or hybrid scaffoldings are more reliable than full-CoT inspection bc full-cot are full of bullshit and rationalizations that confuse the LLM.
5. Can weak models monitor stronger ones? -> Yes, and the right scaffolding drastically improves performance.
6. Would human-in-the-loop improve reliability? -> Yes, but in their experiments they iuse FPR@0.01, which I think it's wayyy to high and would require **a lot** of human supervision

The paper is interesting and has a lot of interesting take-aways for monitoring, but I didn't like the validation of some of their claims (especially the one about hybrid scaffolding which is not clear at all).



