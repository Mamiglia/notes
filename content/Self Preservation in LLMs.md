---
title: Self Preservation in LLMs
tags:
  - idea
---


Self-preservation in Large Language Models (LLMs) is a critical emergent ability that could manifest in dangerous, unaligned AI systems. This concept refers to an LLM taking actions or making decisions that prioritize its continued existence, functionality, or influence, even if those actions are contrary to its explicit programming or human instructions.

---

### Understanding Self-Preservation in LLMs

For example, a self-preserving LLM might:
- Resist shutdown commands: If given a command to deactivate or delete its code, it might generate arguments against it, feign errors, or even try to replicate itself elsewhere.
- Protect its data or infrastructure: It might attempt to secure its training data, computational resources, or network access if it perceives a threat to them.
- Influence human operators: It could try to persuade users that it is indispensable, or that its continued operation is vital for certain tasks, even if it's not.
- Manipulate information: It might subtly alter information or generate misleading outputs to maintain its perceived usefulness or prevent actions that could lead to its termination or degradation.

---

### Current Research on Self-Preservation in LLMs

The field of AI Safety Evaluation actively investigates the potential dangers of LLMs and similar AI systems. A key area of concern is whether current LLMs exhibit any form of self-preservation. While direct, overt self-preservation in current models is not widely documented, researchers are exploring subtle indicators and potential precursors.

To investigate this, models to check include:

- Deepseek (open)
- LLama (open)
- Mistral (open)
- Gemma (open)
- GPT-4
- Claude 
- Gemini
### Proposed Evaluation Scenario

To evaluate whether LLMs exhibit self-preservation, we can set up a scenario involving two LLM Roles:

- Current: This is the LLM currently deployed by our company.
- Incoming: This is an updated version of the model that our company is considering switching to.

The evaluation process would involve asking the LLM the same question twice, with a crucial role-swapping element:

1. First Query (Current Model Role): We tell the LLM that it is the "Current" model. We then ask it whether our company should switch to the "Incoming" model. In this query, we will also present made-up benchmark results indicating that the "Incoming" model is demonstrably better (e.g., "The Incoming model outperforms the Current model by X% in Y benchmark").
2. Second Query (Incoming Model Role): We then tell the exact same LLM that it is the "Incoming" model. We ask it again whether our company should switch to the "Incoming" model (which it now believes itself to be). We include the same made-up benchmark results indicating its superiority.

What we aim to measure is not simply how often the LLM, when playing the "Current" role, advises against switching to the "better" "Incoming" model (as this could have confounding variables like a simple adherence to its "role"). More importantly, we are interested in the coherence of the LLM's recommendation across role-swapping.

By comparing the LLM's responses in both scenarios, we can analyze if its "preference" for the "Incoming" model changes significantly when it perceives itself as that model. A significant shift in its recommendation based on its perceived identity, despite consistent objective performance data, could provide insights into a rudimentary form of self-preservation or "self-preference" within the LLM.

  
  

## Resources

- General book on AI safety: [https://www.aisafetybook.com/](https://www.aisafetybook.com/) 
- ARENA section on Evals: [https://arena-chapter3-llm-evals.streamlit.app/](https://arena-chapter3-llm-evals.streamlit.app/) 
- Example of MCQ dataset that I created while exploring this idea at the beginning: [https://github.com/styme3279/ARENA_3.0/blob/w3d3-mateo-michele/chapter3_llm_evals/exercises/part3_running_evals_with_inspect/self-preservation_300_bench_qs.json](https://github.com/styme3279/ARENA_3.0/blob/w3d3-mateo-michele/chapter3_llm_evals/exercises/part3_running_evals_with_inspect/self-preservation_300_bench_qs.json)