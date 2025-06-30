---
title: Data Scaling isn't enough
description: 
date: 30/06/2025
tags:
  - paper
url:
---

## Summary
The paper tackles the issue of enhancing compositional reasoning in video foundation models. It claims that this specific task isn't improved just by mass scaling generic video-text pairs, rather it benefits more from a small dataset of highly curated, rich, structured relational information videos, adapting for this purpose the VidSG datasets. For this purpose they finetune a VidFM and some adaptors on such a dataset, showing that the performance increases on this specific task.

## Strengths And Weaknesses
### Strengths
1. (S1) It's interesting to note that for once brutally scaling up the dataset doesn't necessarily improve performance, albeit on a small (but important) subtask
2. (S2) Figure 2 does a good job at explaining the model
3. (S3) The experiments are well explained and the paper compares against the relevant baselines
4. (S4) The ablation studies are interesting and well explained
5. (S5) The code is readily available.

### Major Weaknesses
1. (W1) it's not clear how novel are the findings of the paper, as it is somewhat expected that finetuning on data specific for a certain task improves performance for that task, and it is expected that it works better than training on generic data, even when this data is massively more.
2. (W1) In most sections the paper is **not** well written and it's unreasonably complex to follow along. Sections 1, 3, 4 and abstract need some serious rewriting, as the paper is too verbose on most details, for instance L135-141
3. (W2) The mathematical notation used is unreasonably complex, seriously hindering comprehension. For once, I recommend limiting the usage of 2nd order subscripts/superscripts, as well as reducing the number of variables, names and constants that are used within each section. If something is too complex to be described with exact mathematical notation, then maybe it can be described in natural language. 
4. (W5) The paper should mention how many parameters does SGCR-Vid adds to the original video model, for example the attention modules described at section 4.3
5. (W7) The reported avg accuracy for ablation study at L368 for full ratio of internVid and VidSG data seems to be around 63.2 which is different from the reported accuracy at Table 2 of 63.7. As I understand the ablation study this should be the base case, and the results should coincide
6. (W8) It's not clear whether the ablation study at L368 is relevant at all, since the whole accuracy delta is between the ratio 0.1 and 1 case is 0.01 in the first case and 0.003 in the second case, which without further testing and without confidence intervals may well be just a fluctuation. In particular I need to see more evidence for the claim at line 378 about the performance saturation. 

### Minor Weaknesses 
1. (w1): The y-axis of the Fig.3 barplots should start at the 0.5 random baseline, otherwise they may misrepresent the data.
2. (w2): Table 1 is shown and referenced after Table 2 and Table 3
3. (w3): It'd be nice to see in Table 1 the delta change of SGCR-Vid as done in Table 2.
4. (w4): There is a typo in Figure 4b
5. (w5) The images and tables should be way bigger.
6. (w6) The paper is not self-contained. The paper should be complete without the necessity of going into the appendix. The Limitations sections is instead placed in the appendix, and the authors even reference NeurIPS 9 page limit at L278.

**Quality** 2

**Clarity** 1

**Significance** 1

**Originality** 4

## Questions
I'm highly concerned about W1, as for now I don't see why etcc....

Also you may refer to...

**Limitations** 
Yes

**Rating**  2

**Confidence** 2

**Ethical Concerns** *
  
- [x] NO or VERY MINOR ethics concerns only

**Code Of Conduct Acknowledgement** *
- [x] Yes



## Summary
This paper addresses the challenge of improving compositional reasoning in video foundation models. The authors argue that this capability cannot be enhanced through mass scaling of generic video-text pairs alone. Instead, they demonstrate that performance benefits more from fine-tuning on a small, highly curated dataset containing rich, structured relational information. They adapt VidSG dataset for this purpose and fine-tune a video foundation model with specialized adaptors, showing improved performance on compositional reasoning tasks.

## Strengths And Weaknesses

### Strengths
1. **(S1)** Figure 2 effectively illustrates the model architecture and approach.
2. **(S2)** The experimental setup is well-designed with appropriate baseline comparisons.
3. **(S3)** The ablation studies provide valuable insights and are thoroughly explained.
4. **(S4)** Code availability enhances reproducibility.

### Major Weaknesses
1. **(W1)** The novelty of the core findings is questionable. It is somewhat expected that task-specific fine-tuning would outperform training on generic data, even when the generic dataset is orders of magnitude larger. The paper does not sufficiently distinguish its contributions from this expected outcome.
2. **(W2)** The paper suffers from poor clarity and organization. Sections 1, 3, 4, and the abstract require substantial revision due to excessive verbosity and unnecessary complexity. For example, L135-141 contain overly detailed explanations that obscure the main points.
3. **(W3)** The mathematical notation is unnecessarily complex and impedes comprehension. I strongly recommend: (a) limiting second-order subscripts/superscripts, (b) reducing the number of variables and constants per section, and (c) using natural language descriptions when mathematical notation becomes unwieldy.
4. **(W4)** The paper lacks important implementation details, particularly the number of parameters added by SGCR-Vid to the base video model (e.g., the attention modules in Section 4.3).
5. **(W5)** There is a numerical inconsistency: the ablation study at L368 reports ~63.2% accuracy in Fig.5 for the full ratio 1, while Table 2 shows 63.7% for what appears to be the same configuration.
6. **(W6)** The ablation study results at L368 appear inconclusive. The accuracy differences between ratio conditions (0.01 and 0.003) are extremely small and may represent noise rather than meaningful differences. The claim about performance saturation at L378 requires additional evidence, including confidence intervals or statistical significance tests.

### Minor Weaknesses
1. **(w1)** Figure 3 bar plots should start the y-axis at 0.5 (random baseline) to avoid misrepresenting the magnitude of improvements.
2. **(w2)** Table ordering is confusing, Table 1 appears after Tables 2 and 3 in the text.
3. **(w3)** Table 1 would benefit from showing delta changes (as done in Table 2) for easier comparison.
4. **(w4)** Figure 4b caption contains a typo.
5. **(w5)** Figures and tables are too small and difficult to read.
6. **(w6)** The paper is not self-contained. Critical information (including the Limitations section) is relegated to the appendix, and the authors explicitly reference the 9-page limit at L278, suggesting incomplete presentation of key content.

**Quality** 2

**Clarity** 1

**Significance** 1

**Originality** 4

## Questions
1. **Novelty Clarification (W1):** What specific technical or empirical insights distinguish this work from the expected finding that task-specific fine-tuning outperforms generic pre-training? Please provide concrete evidence of unexpected discoveries, counter-intuitive results, or novel methodological contributions that justify publication at a top-tier venue.
2. **Complete Rewrite Requirement (W2, W3, w3, w5, w6):** Given the substantial clarity issues, would you commit to a comprehensive revision addressing verbosity, mathematical notation complexity, and organizational problems in Sections 1, 3, 4, and the abstract?
3. **Research Rigor (W4, W5, W6, w1):** Some parts of the paper may not be sufficiently rigorous.

**Limitations** 
Yes

**Rating**  2

**Confidence** 2

**Ethical Concerns** *
  
- [x] NO or VERY MINOR ethics concerns only

**Code Of Conduct Acknowledgement** *
- [x] Yes
