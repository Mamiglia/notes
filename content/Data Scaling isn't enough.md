---
title: Data Scaling isn't enough
description: 
date: 30/06/2025
tags:
  - paper
url:
---

## Summary
*Briefly summarize the paper and its contributions. This is not the place to critique the paper; the authors should generally agree with a well-written summary. This is also not the place to paste the abstract—please provide the summary in your own understanding after reading.*
The paper tackles the issue of enhancing compositional reasoning in video foundation models. It claims that this specific task isn't improved just by mass scaling generic video-text pairs, rather it benefits more from a small dataset of highly curated, rich, structured relational information videos, adapting for this purpose the VidSG datasets. For this purpose they finetune a VidFM and some adaptors on such a dataset, showing that the performance increases on this specific task.

## Strengths And Weaknesses
*Please provide a thorough assessment of the strengths and weaknesses of the paper. A good mental framing for strengths and weaknesses is to think of reasons you might accept or reject the paper. Please touch on the following dimensions: Quality, Clarity, Significance, and Originality. For more information, please see the NeurIPS 2025 Reviewer Guidelines (https://neurips.cc/Conferences/2025/ReviewerGuidelines). You can incorporate Markdown and LaTeX into your review. See https://openreview.net/faq.*
### Strengths
1. (S1) It's interesting to note that for once brutally scaling up the dataset doesn't necessarily improve performance, albeit on a small (but important) subtask
2. (S2) Figure 2 does a good job at explaining the model
3. (S3) The experiments are well explained and the paper compares against the relevant baselines
4. (S4) The ablation studies are interesting and well explained

### Major Weaknesses
1. (W1) In most sections the paper is **not** well written and it's unreasonably complex to follow along. Sections 1, 3, 4 and abstract need some serious rewriting, as the paper is too verbose on most details. 
2. (W2) The mathematical notation used is unreasonably complex, seriously hindering readability. For once, I recommend limiting the usage of 2nd order subscripts/superscripts, as well as reducing the number of variables, names and constants that are used within each section. If something is too complex to be described with exact mathematical notation, then maybe it can be described in natural language. 
3. (W3) it's not clear how novel are the findings of the paper, as it is somewhat expected that finetuning on data specific for a certain task improves performance for that task, and it is expected that it works better than training on generic data, even when this data is massively more.
4. (W4) The images and tables should be way bigger.
5. (W5) The paper should mention how many parameters does SGCR-Vid adds to the original video model, for example the attention modules described at section 4.3
6. (W6) The paper should be complete without the necessity of going into the appendix. The Limitations sections is instead placed in the appendix, and the authors even reference NeurIPS 9 page limit at line 278.
7. (W7) The reported avg accuracy for ablation study at line 368 for full ratio of internVid and VidSG data seems to be around 63.2 which is different from the reported accuracy at Table 2 of 63.7. As I understand the ablation study this should be the base case, and the results should coincide
8. (W8) It's not clear whether the ablation study at line 368 is relevant at all, since the whole accuracy delta is between the ratio 0.1 and 1 case is 0.01 in the first case and 0.003 in the second case, which without further testing and without confidence intervals may well be just a fluctuation. In particular I need to see more evidence for the claim at line 378 about the performance saturation. 

### Minor Weaknesses 
1. (w1): The y-axis of the Fig.3 barplots should start at the 0.5 random baseline, otherwise they may misrepresent the data.
2. (w2): Table 1 is shown and referenced after Table 2 and Table 3
3. (w3): It'd be nice to see in Table 1 the delta change of SGCR-Vid as done in Table 2.
4. (w4): There is a typo in Figure 4b
5. (??) The authors should assume that the reader has some previous knowledge about deep learning. For example there is no need to define what a CLS token is (L135), that "`,`" is the concatenation operator and to define the softmax operation (Eq.2)

**Quality**
Based on what you discussed in "Strengths and Weaknesses", please assign the paper a numerical rating on the following scale to indicate the quality of the work.

    [] 4: excellent
    [] 3: good
    [] 2: fair
    [] 1: poor

**Clarity** *
Based on what you discussed in "Strengths and Weaknesses", please assign the paper a numerical rating on the following scale to indicate the clarity of the paper.

    [] 4: excellent
    [] 3: good
    [] 2: fair
    [] 1: poor

**Significance** *
Based on what you discussed in "Strengths and Weaknesses", please assign the paper a numerical rating on the following scale to indicate the significance of the paper.

    [] 4: excellent
    [] 3: good
    [] 2: fair
    [] 1: poor

**Originality** *
Based on what you discussed in "Strengths and Weaknesses", please assign the paper a numerical rating on the following scale to indicate the originality of the paper.

    [] 4: excellent
    [] 3: good
    [] 2: fair
    [] 1: poor

**Questions** *
Please list up and carefully describe questions and suggestions for the authors, which should focus on key points (ideally around 3–5) that are actionable with clear guidance. Think of the things where a response from the author can change your opinion, clarify a confusion or address a limitation. You are strongly encouraged to state the clear criteria under which your evaluation score could increase or decrease. This can be very important for a productive rebuttal and discussion phase with the authors.

**Limitations** *
Have the authors adequately addressed the limitations and potential negative societal impact of their work? If so, simply leave “yes”; if not, please include constructive suggestions for improvement. In general, authors should be rewarded rather than punished for being up front about the limitations of their work and any potential negative societal impact. You are encouraged to think through whether any critical points are missing and provide these as feedback for the authors.

**Rating** *
Please provide an "overall score" for this submission.

    [] 6: Strong Accept: Technically flawless paper with groundbreaking impact on one or more areas of AI, with exceptionally strong evaluation, reproducibility, and resources, and no unaddressed ethical considerations.
    [] 5: Accept: Technically solid paper, with high impact on at least one sub-area of AI or moderate-to-high impact on more than one area of AI, with good-to-excellent evaluation, resources, reproducibility, and no unaddressed ethical considerations.
    [] 4: Borderline accept: Technically solid paper where reasons to accept outweigh reasons to reject, e.g., limited evaluation. Please use sparingly.
    [] 3: Borderline reject: Technically solid paper where reasons to reject, e.g., limited evaluation, outweigh reasons to accept, e.g., good evaluation. Please use sparingly.
    [] 2: Reject: For instance, a paper with technical flaws, weak evaluation, inadequate reproducibility and incompletely addressed ethical considerations.
    [] 1: Strong Reject: For instance, a paper with well-known results or unaddressed ethical considerations.

**Confidence** *
Please provide a "confidence score" for your assessment of this submission to indicate how confident you are in your evaluation.
    
    [] 5: You are absolutely certain about your assessment. You are very familiar with the related work and checked the math/other details carefully.
    [] 4: You are confident in your assessment, but not absolutely certain. It is unlikely, but not impossible, that you did not understand some parts of the submission or that you are unfamiliar with some pieces of related work.
    [] 3: You are fairly confident in your assessment. It is possible that you did not understand some parts of the submission or that you are unfamiliar with some pieces of related work. Math/other details were not carefully checked.
    [] 2: You are willing to defend your assessment, but it is quite likely that you did not understand the central parts of the submission or that you are unfamiliar with some pieces of related work. Math/other details were not carefully checked.
    [] 1: Your assessment is an educated guess. The submission is not in your area or the submission was difficult to understand. Math/other details were not carefully checked.  

**Ethical Concerns** *
If there are significant ethics concerns that require review by an ethics expert, please flag the paper with all major concerns for an ethics review; if not, only select the first checkbox. For guidance on when this is appropriate, please review the NeurIPS ethics guidelines (https://neurips.cc/public/EthicsGuidelines).
    
    [] NO or VERY MINOR ethics concerns only
    [] Major Concern: Improper research involving human subjects
    [] Major Concern: Data privacy, copyright, and consent
    [] Major Concern: Data quality and representativeness
    [] Major Concern: Safety and security
    [] Major Concern: Discrimination, bias, and fairness
    [] Major Concern: Deception and harassment
    [] Major Concern: Environmental impact
    [] Major Concern: Human rights (including surveillance)

**Code Of Conduct Acknowledgement** *
While performing my duties as a reviewer (including writing reviews and participating in discussions), I have and will continue to abide by the NeurIPS Code Of Conduct (https://neurips.cc/public/CodeOfConduct) and policy on NeurIPS Reviewer Integrity (https://neurips.cc/Conferences/2025/ReviewerIntegrity).
    
    [] Yes
