---
title: Untitled
description: 
date: 16/04/2025
tags:
  - unprocessed
url:
---
The paper introduces a novel framework for Human Pose Estimation (HPE) which is divided into two stages: 
1. Estimation of specific frames among all the frames (keyframes)
2. Joint displacement prediction in the "middle" frames
Motivating this by claiming that other HPE estimators suffer from error propagation among long sequences which result in incoherent motions. The authors claim that this top-down approach can instead improve HPE as the first stage needs to model only a generic version of the motion, while the latter models the small movements between one frame and another.

The paper describes in detail the architecture and experiments, and conducts ablation studies on their proposed usage of the Spatio-Temporal transformer.

### Strenghts
- S1: the two-stage approach allows modularity and training-parallelization for the models.
- S2: The proposed approach slightly improves on others on the MPI-INF-3DHP dataset
- S3: The paper is clear and easy to read.

### Major Weaknesses
- W1: The proposed approach barely matches the performances of previous methods on quantitative tests on H36M. This is a huge issue to me as this puts in question the value of the paper as a whole. Since the authors claim that "the proposed MvP can produce realistic and consistent trajectories for varying actions and joints" (line 450) than I expect at least some quantitative result to back up this claim. For example the authors might report the average trajectory error of previous models compared to MvP.
- W?: Most papers on diffusion models on HPE usually report both deterministic methods along with multi-hypothesis. In this papers the authors include the multi-hypothesis only in the supplementary material, and don't confront their score with those of other papers, not even D3DP. This is relevant as MvP score (40.18), is way worse than D3DP (35.4).  
- W?: Also the authors don't mention the current state of the art in HPE, namely KTPFormer [ADD CITATION].
- W2: In the ablation study on "Interval Length" the authors provide results for length 3, 9, 27, with 3 being the best one. Following my understanding of the MvP, this means that the "displacement denoiser" needs to predict only 2 frames (one is given), which feels like a suspiciously low amount of frames to predict. 
- W3: In the same ablation study, since the lowest number is also the best one I would expect the authors to try even lower numbers. I especially would like to see what would happen with length=1, as this would be like predicting all the frames in one go (going back to 1-stage only estimators). What I mean with this is that I'm skeptical that the displacement denoiser is doing any actual work, and I'd like to see the authors confute my hypothesis.
- W4: In the ablation study on "Attention Order" (Table 4) the temporal-then-spatial approach improves by just 0.22mm (-0.54%), a value so small that without any confidence value questions the validity of the ablation. It seems difficult for this difference to be statistically significant, and not just a result of random fluctuations.
- W5: At line 393 the authors explain that in order to resolve conflicts between accumulated displacement and the next keyframe they "preserve the results predicted by the keyframe denoiser while discard the aggregated poses". To me this seems like a missed opportunity and possibly a mistake. As I currently understand, this means that every $n_c$ frames the estimated pose snaps to the next keyframe without any regard of flow or motion coherence. I believe that the authors should fundamentally change their framework to somehow account for a smooth transition between keyframes, possibly by accounting for some kind of "fancy interpolation".
- W6: Line 184: "we introduce the concept of relative joint displacement, referring to the joint movement between two neighbor frames". I believe that this phrasing is wrong and might get the idea that this is an original proposal from the authors, while predicting joint displacement is not a novel idea, but rather a long-time industry standard [CITE PAPERS HERE]
- W7: Eq.13 introduces an additional loss term that punishes huge displacements between neighboring frames. If the intuition of the authors about drastic sub-second movements being unlikely than I believe that this term is useless as it's already encoded by the natural distribution of the data. I'd like to see at least an ablation study to prove that this term is effectively useful.
- W8: Nowhere in the paper the authors mention the compute power necessary for this two stage approach. A fair comparison with previous approaches necessitates knowing how many parameters each of the denoisers have in comparison with previous methods, and also whether this two stage approach slows down inference times.
  
### Minor Weaknesses
- w1: The notation used in eq. 5 and 6 should follow other academic papers in describing the backward diffusion process, rather than using fantasious and uninformative symbols like $D_{displacement}$.
- w2: The eq. 8 and 9 are uninformative. Figure 3 does a much better job at describing the same architecture.
- w3: The text is somewhat too technical in some specific sections, like 289-298, 420-429. These kind of details are better left in the appendix to improve readability.
- w4: The qualitatives shown are difficult to interpret the chosen visualizations are unintuitive.

### Preliminary Recommendation
1: Reject

### Preliminary Justification
While I do believe that this two-stage approach has the potential on improving HPE, especially on long motion sequences, the paper fails to demonstrate significant advantages over existing methods. The quantitative results barely match previous approaches, which directly contradicts the authors' claims about reducing error propagation. Several critical issues remain unaddressed:

1. The suspiciously short optimal interval length (3 frames) raises questions about whether the displacement denoiser is truly necessary or effective.
2. The lack of statistical significance testing for the attention order ablation study makes it difficult to determine if the reported improvements are meaningful.
3. The abrupt transition between keyframes by discarding accumulated displacement likely creates motion incoherence - ironically the very problem this paper claims to solve.
4. Claims of novelty regarding relative joint displacement are overstated, as this is an established approach in the field.

Without addressing these major weaknesses, the paper does not make a convincing case -- for its approach --cambia. While the paper is clear and the two-stage framework offers potential modularity benefits, the lack of demonstrable performance improvements over existing methods undermines its contribution to the field. -And novelty community-

### Confidence Level
3

---

**Summary**  
The paper introduces a novel framework for Human Pose Estimation (HPE) which is divided into two stages: 
1. Estimation of specific frames among all the frames (keyframes)
2. Joint displacement prediction in the "middle" frames
Motivating this by claiming that other HPE estimators suffer from error propagation among long sequences which result in incoherent motions. The authors claim that this top-down approach can instead improve HPE as the first stage needs to model only a generic version of the motion, while the latter models the small movements between one frame and another.

The paper describes in detail the architecture and experiments, and conducts ablation studies on their proposed usage of the Spatio-Temporal transformer.

---

### Strengths

- **S1**: The two-stage design allows modularity, potentially enabling independent training and simplifying the learning problem.
- **S2**: The method demonstrates slight improvements over existing approaches on the MPI-INF-3DHP dataset.
- **S3**: The manuscript is generally well-written and easy to follow.

---

### Major Weaknesses

- **W1**: On the widely-used H36M benchmark, the proposed method performs on par or slightly worse than existing approaches. This weakens the empirical support for the core claims "the proposed MvP can produce realistic and consistent trajectories for varying actions and joints" (line 450). It would strengthen the work to include trajectory-level quantitative metrics (such as average trajectory error) and directly compare with established baselines.
- **W2**: The discussion around multi-hypothesis prediction is limited. While this variant is included in the supplementary material, its omission from the main results and lack of comparison to strong baselines (e.g., D3DP) makes it difficult to fully assess the method’s performance. For instance, MvP’s multi-hypothesis score (40.18) falls behind D3DP (35.4), and this discrepancy is not addressed in the main paper.
- **W3**: The paper does not discuss recent state-of-the-art models in HPE, notably KTPFormer [add citation]. A clearer contextualization of the method within the current landscape would be beneficial.
- **W4**: In the ablation study on “Interval Length” (Table 5) the best results are achieved with a length of 3 (among 27, 9 and 3). This setting implies that the second-stage model is effectively predicting only two intermediate frames, which raises concerns about the actual contribution of the displacement denoiser. This warrants further investigation and clarification.
- **W5**: Also in Table 5, given that shorter intervals lead to better results, it would be informative to report performance for even smaller values (e.g., length = 1). This could help evaluate whether the two-stage design provides meaningful advantages over single-stage approaches.
- **W6**: The ablation on "Attention Order" (Table 4) shows a marginal improvement of 0.22mm (−0.54%), which is very small. Without confidence intervals or significance tests, it's difficult to assess whether this is a meaningful gain rather than noise.
- **W7**: At line 393 the authors explain that in order to resolve conflicts between accumulated displacement and the next keyframe they "preserve the results predicted by the keyframe denoiser while discard the aggregated poses". This strategy could lead to abrupt transitions and may undercut the method’s stated goal of producing temporally coherent motion. A more principled transition mechanism could enhance the method's overall consistency.
- **W8**: The phrase “we introduce the concept of relative joint displacement…” (line 184) could be misleading, as this is a well-established concept in HPE literature. It would be more accurate to clarify the specific novelty in the context of this work, if any. [CITEEEE]
- **W9**: Equation 13 introduces a loss term penalizing large displacements between frames. If the data naturally adheres to smooth motion assumptions, it's unclear whether this term provides tangible benefits. An ablation to evaluate its contribution would be helpful.
- **W10**: The paper lacks discussion on computational efficiency. Given that the method uses two denoisers, it is important to report model size, runtime, and parameter count, especially when comparing with single-stage baselines.

---

### Minor Weaknesses

- **w1**: The notation in Equations 5 and 6 could benefit from closer alignment with standard diffusion model literature. Custom symbols like $\mathcal{D}_{displacement}$ are not particularly informative.
- **w2**: The eq. 8 and 9 are uninformative. Figure 3 does a much better job at describing the same architecture.
- **w3**: Some sections (e.g., 289–298, 420–429) delve into technical detail that might be better suited for the appendix to maintain flow. 
- **w4**: The qualitative results are difficult to interpret due to unclear or unintuitive visualizations.

In general it feels like a lot of details could be removed to make space for more relevant infomation, as addressed in W1, W2, W9.



The proposed two-stage framework for Human Pose Estimation offers an interesting and potentially valuable direction, particularly for handling long motion sequences where error propagation can become a challenge. However, the paper does not convincingly demonstrate that this approach yields substantial improvements over existing methods.

Several critical concerns remain unresolved:
- Weak performance on H36M, which contradicts the claim of improved long-term coherence (W1).
- Multi-hypothesis results are weaker than prior work and underreported, with no direct comparison to strong baselines like D3DP (W2).
- Lack of comparison to current state-of-the-art methods such as KTPFormer (W3).
- Minimal gains in ablation studies (e.g., attention order) without statistical testing, limiting interpretability (W4).

Overall, while the paper is clearly written and the proposed modular design offers some conceptual advantages, the current empirical results do not provide sufficient evidence of performance gains or methodological novelty compared to existing state-of-the-art approaches. I encourage the authors to further investigate the role and contribution of each component, provide stronger baselines and statistical analysis, and clarify the method’s place within the broader HPE landscape.
