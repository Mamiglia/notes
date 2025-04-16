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
- the two-stage approach allows modularity and training-parallelization for the models.
- The proposed approach slightly improves on others on the MPI-INF-3DHP dataset
- The paper is clear and easy to read.

### Major Weaknesses
titola:
- The proposed approach barely matches the performances of previous methods on any quantitative test. This is a huge issue to me as this puts in question the value of the paper as a whole. Since the authors claim that this model should improve on the error propagation of previous approaches than I expect at least some quantitative result to back up this claim. 
- In the ablation study on "Interval Length" the authors provide results for length 3, 9, 27, with 3 being the best one. This means that the "displacement denoiser" needs to predict only 2 frames (one is given), which feels like a suspiciously low amount of frames to predict. 
- In the same ablation study, since the lowest number is also the best one I would expect the authors to try even lower numbers. I especially would like to see what would happen with length=1, as this would be like predicting all the frames in one go (going back to 1-stage only estimators). What I mean with this is that I'm skeptical that the displacement denoiser is doing any actual work, and I'd like to see the authors confute my hypothesis.
- In the ablation study on "Attention Order" the authors don't report confidence values for the attention order. I'd like the authors to check whether this difference is statistically significant, and not just a result of random fluctuations. Table 4
- At line 393 the authors explain that they resolve conflicts between accumulated displacement and the next keyframe by just discarding the displacement and considering only the keyframe. To me this seems like a missed opportunity and possibly a mistake. If I understood this correctly this means that every $n_c$ frames the estimated pose snaps to the next keyframe without any regard of flow or  motion coherence. I believe that the authors should fundamentally change their framework to somehow account for a smooth transition between keyframes, possibly by accounting for some kind of "fancy interpolation".
- At line 184 the authors write that "we introduce the concept of relative joint displacement, referring to the joint movement between two neighbor frames". I believe that this phrasing is wrong and might get the idea that this is an original proposal from the authors, while predicting joint displacement is not a novel idea, but rather a long-time industry standard [CITE PAPERS HERE]
- Eq.13 introduces an additional loss term that punishes huge displacements between neighboring frames. If the intuition of the authors about drastic sub-second movements being unlikely than I believe that this term is useless as it's already encoded by the natural distribution of the data. I'd like to see at least an ablation study to prove that this term is effectively useful.  (meno cattivo)
- Nowhere in the paper the authors mention the compute power necessary for this two stage approach. A fair comparison with previous approaches necessitates knowing how many parameters each of the denoisers have in comparison with previous methods, and also whether this two stage approach slows down inference times.
- ### Minor Weaknesses
- The notation used in eq. 5 and 6 should follow other academic papers in describing the backward diffusion process, rather than using fantasious and uninformative symbols like $D_{displacement}$.
- The eq. 8 and 9 are uninformative. Figure 3 does a much better job at describing the same architecture.
- The text is somewhat too technical in some specific sections, like 289-298, 420-429. These kind of details are better left in the appendix to improve readability.
- The qualitatives shown are difficult to interpret the chosen visualizations are unintuitive.


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

