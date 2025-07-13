---
title: Iterative Soft Autoregressive Decoding
description: 
date: 11/06/2024
tags:
  - idea
status: active/draft
url:
---
### Summary

We propose a decoding scheme where, instead of committing to the top-1 token at each step, the model receives a **soft embedding**, a weighted average of the top-k predicted tokens. Then, in subsequent steps, the model **re-evaluates and updates** the last $k$ tokens by re-feeding them as soft inputs. This process is repeated for a fixed number of refinement rounds.

The goal: allow **uncertain or ambiguous tokens** to be **gradually refined**, giving the model the opportunity to "correct itself" over multiple passes.

---

### Motivation

Standard autoregressive decoding is greedy: once a token is chosen, it becomes immutable input. But language is uncertain. Why not let the model revise recent decisions?

This method:

- Doesn’t require retraining
- Doesn’t introduce auxiliary models
- Only modifies inference-time logic
- Exploits existing uncertainty (e.g. softmax scores) as signal for refinement

---

### Related Work

|Idea|Exists?|Notes|
|---|---|---|
|**Soft token embeddings**|✅ MoI (Zhuang et al., 2024)||
|**Deliberation / self-correction**|✅ Requires training or second model||
|**Speculative decoding / draft tokens**|✅ Uses fast draft model and verifier||
|**Multi-pass decoding within base model**|❌ Not explored||

---

### Novelty

- **Inference-time iterative refinement** of recent tokens using only the base model
- **Soft re-feeding of prior outputs**, enabling uncertainty propagation
- **No architectural changes** or retraining required
- Simple to implement; compatible with existing transformers

---

### Open Questions

- How many past tokens should be refined ($k$)?
- How many refinement passes are optimal?
- Can entropy or logit variance guide when to re-evaluate?
- How does it compare to top-k sampling or beam search in quality and compute?