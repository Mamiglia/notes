---
title: Model Stitching for Data Scarcity
tags:
  - idea
---
The research idea, "Platonic Stitching," proposes a novel framework to overcome the significant challenge of labeled data scarcity in **text-to-X (T2X) generation**, where 'X' is an unspecified modality. This approach aims to improve T2X models beyond what's possible by training solely on limited labeled data.

The core concept involves **model stitching** and the **Platonic Representation Hypothesis (PRH)**. The PRH suggests that neural networks, even when trained on different modalities or datasets, can converge towards a shared statistical model of reality.1 Platonic Stitching leverages this by connecting a powerful **pre-trained Large Language Model (LLM)** with an **encoder for modality X**.

Given a decent amount of unlabeled data of modality X and a small portion of labeled text-to-X data, the framework will:

1. Train the X-modality encoder using **self-supervised learning** on the abundant unlabeled X data.
2. Align the latent representations of the LLM and the X-encoder, implicitly discovering the shared "Platonic" space. This alignment can be achieved using **parameter-efficient model stitching techniques** like adapter modules 4, and **semi-supervised methods** such as pseudo-labeling.
3. Refine the T2X generation using the small labeled dataset through **few-shot learning**.

By harnessing the LLM's robust semantic understanding and the structural coherence implied by PRH, this approach is expected to significantly enhance T2X generation capabilities, leading to improved generalization and reduced overfitting compared to traditional methods that rely only on scarce labeled data.4

**Related Resources:**

- **Original Concept (Platonic Representation Hypothesis & Model Stitching):** 1 ([https://arxiv.org/abs/2106.07682](https://arxiv.org/abs/2106.07682))    
- [https://arxiv.org/pdf/1902.00751](https://arxiv.org/pdf/1902.00751)
- [https://arxiv.org/pdf/2506.12379](https://arxiv.org/pdf/2506.12379)
- [https://arxiv.org/abs/2507.01098](https://arxiv.org/abs/2507.01098)
- [https://arxiv.org/abs/2502.05568](https://arxiv.org/abs/2502.05568)
- [https://arxiv.org/pdf/2202.12040](https://arxiv.org/pdf/2202.12040)
- [https://arxiv.org/html/2402.01204v4](https://arxiv.org/html/2402.01204v4)
- [https://arxiv.org/pdf/2401.01736](https://arxiv.org/pdf/2401.01736)
- [https://arxiv.org/pdf/2402.01440](https://arxiv.org/pdf/2402.01440)
- [https://www.arxiv.org/pdf/2412.11694v2](https://www.arxiv.org/pdf/2412.11694v2)
- [https://arxiv.org/html/2506.04788v1](https://arxiv.org/html/2506.04788v1)
- [https://arxiv.org/html/2412.17836v1](https://arxiv.org/html/2412.17836v1)