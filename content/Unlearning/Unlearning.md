---
title: Unlearning
description: 
date: 11/08/2024
tags:
  - idea
status: active/draft
url:
---
Machine unlearning is the practice of erasing information from machine learning models. It's essential for AI safety as information erasure is imperative for releasing strong models to the public, to contain and avoid possible harm by malicious users.

List of papers:
- **MACE** http://arxiv.org/abs/2403.06135
- **ESD** http://arxiv.org/abs/2303.07345
- **RECE** http://arxiv.org/abs/2407.12383
- 
```embed
title: "Does your LLM truly unlearn? An embarrassingly simple approach to recover unlearned knowledge"
image: "http://arxiv.org/static/browse/0.3.4/images/arxiv-logo-fb.png"
description: "Large language models (LLMs) have shown remarkable proficiency in generating text, benefiting from extensive training on vast textual corpora. However, LLMs may also acquire unwanted behaviors from the diverse and sensitive nature of their training data, which can include copyrighted and private content. Machine unlearning has been introduced as a viable solution to remove the influence of such problematic content without the need for costly and time-consuming retraining. This process aims to erase specific knowledge from LLMs while preserving as much model utility as possible. Despite the effectiveness of current unlearning methods, little attention has been given to whether existing unlearning methods for LLMs truly achieve forgetting or merely hide the knowledge, which current unlearning benchmarks fail to detect. This paper reveals that applying quantization to models that have undergone unlearning can restore the “forgotten” information. To thoroughly evaluate this phenomenon, we conduct comprehensive experiments using various quantization techniques across multiple precision levels. We find that for unlearning methods with utility constraints, the unlearned model retains an average of 21\\% of the intended forgotten knowledge in full precision, which significantly increases to 83\\% after 4-bit quantization. Based on our empirical findings, we provide a theoretical explanation for the observed phenomenon and propose a quantization-robust unlearning strategy to mitigate this intricate issue…"
url: "http://arxiv.org/abs/2410.16454"
```

