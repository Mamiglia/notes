---
title: Untitled
description:
date: 11/02/2026
tags:
  - paper
url: https://arxiv.org/abs/2512.08093
---
OpenAI proposes a new paradigm for Control, where they train the same model first to answer the query and then to report in complete honesty whether there's any dishonesty in the given answer. 

The interesting thing is that they train the same model with 2 reward functions, the normal one for the first answer, and a second one honesty-only for the second one.

They argue that this second reward is very effective **because it's simpler to tell the truth than inventing an elaborate lie**, and since LLMs are just reward-maxing machines they will tend toward this behavior.

They do some small scale test to evaluate this, showing some degree of efficacy in tasks where verification of the honesty is easy (they also argue that in general confessions are easy to verify).



