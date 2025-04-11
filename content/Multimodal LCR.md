**Latent Code Replacement** (LCR) is a technique we developed to erase knowledge in generative models, to steer generation from toxic concepts. 
## Process
- [ ] Read existing literature
- [ ] Review SotA VQ-VAE generative models
- [ ] Prove that these models can generate toxic content
- [ ] Adapt LCR
	- [ ] Make it run
	- [ ] Make it erase
	- [ ] Evaluation
- [ ] Improve LCR

## Literature
### Unlearning
It's important to understand the task of unlearning, why it is important, how to evaluate it and what are the common pitfalls and cases of fake unlearning.
- [Human Motion Unlearning](https://arxiv.org/abs/2503.18674): our paper. Explains LCR.
- [Codebook Interpretability](https://arxiv.org/abs/2310.17230): This is the foundation of LCR 
- [UCE](https://arxiv.org/abs/2308.14761): A zero-shot method for unlearning
- [SalUn](https://arxiv.org/abs/2310.12508): Unlearn method (very cool!)
- [MACE](https://arxiv.org/abs/2403.06135): Unlearn method
- [RMU is mostly shallow](https://www.alignmentforum.org/posts/6QYpXEscd8GuE7BgW/unlearning-via-rmu-is-mostly-shallow): Interesting blog post on a case study of MU reversal
- [SAE Unlearn](https://arxiv.org/abs/2410.19278): 
- [Catastrophic Failure of LLM Unlearning via Quantization](https://arxiv.org/abs/2410.16454): MU reversal
- [Unlearning via Sparse Representations](https://arxiv.org/abs/2311.15268): Paper similar to ours but with wrong premises and invalid tests
### VQ-VAE
Foundational papers on VQ-VAE architecture.
- [VQ-VAE](https://arxiv.org/abs/1711.00937): original paper
- [VQ-VAE 2](https://arxiv.org/abs/1906.00446v1): Follow up on VQ-VAE architecture
- [Soft VQ-VAE](https://arxiv.org/pdf/2412.10958): A middle ground between a VQ and a linear layer

### Generative Models
Here's a list of generative models that use VQ-VAE (or variations). We can apply LCR to one or more of these models.
- [MoMask](https://arxiv.org/abs/2312.00063): Motion forecasting model with RVQ-VAE
- [Janus-Pro](https://github.com/deepseek-ai/Janus/tree/main): Multimodal model that uses VQ-VAE (but I didn't understand where)
- [Sesame](https://www.sesame.com/research/crossing_the_uncanny_valley_of_voice): Conversational Speech Model (yes people do porn also with [this](https://www.reddit.com/r/ChatGPTJailbreak/comments/1j3ztk3/sesame_jailbreak_update/))
- ??? Fill-in with other VQ-VAE models that are SotA (or almost SotA) in their field. 
