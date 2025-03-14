# Block Diffusion: Interpolating between Autoregressive and Diffusion Language Models

- **Published Date**: 2025-03
- **Authors**: Marianne Arriola, Aaron Gokaslan, Justin T Chiu, Zhihan Yang, Zhixuan Qi, Jiaqi Han, Subham Sekhar Sahoo, Volodymyr Kuleshov
- **Link**: https://arxiv.org/abs/2503.09573
- **Summary Date**: 2025-03-14
- **Summarized by**: @wooksong
- **Tool Used**: GPT-4.5
- **Tags**: #DiffusionModels, #BlockDiffusion, #AutoRegressiveModels, #LM, #BD3LM

## Abstract

This work introduces Block Discrete Denoising Diffusion Language Models (BD3-LMs), a novel approach that combines the strengths of autoregressive and diffusion models. Diffusion models, despite their benefits in parallel generation and controllability, face significant limitations in likelihood modeling and generating sequences of variable length.

## Fundamental Concepts

We begin by acknowledging the strengths and weaknesses of the two predominant models:

- **Autoregressive Models (AR):** AR models are highly efficient in likelihood modeling but are inherently sequential, limiting generation speed:

  > "Autoregressive (AR) models define a factorized distribution [...] AR models take L steps to generate L tokens due to sequential dependencies."

- **Discrete Denoising Diffusion Probabilistic Models (D3PM)**: These models excel in parallel token generation but suffer from "fixed-length generation" and "lower likelihood modeling performance":

  > "Discrete diffusion models currently face at least three limitations [...] fixed-length vectors, cannot reuse previous computations, and lag behind autoregressive approaches."

## Introduction of Block Diffusion

Our core innovation is the concept of block diffusion, a method which is both sequential across blocks but parallel within blocks. This allows our model to overcome key limitations by generating arbitrary-length sequences while retaining inference efficiency through KV caching:

> "Block diffusion sequentially generates blocks of tokens by performing diffusion within each block and conditioning on previous blocks."

### Effective BD3-LMs Training

Efficient training of BD3-LMs required addressing significant challenges, notably the "high variance of gradients" during training, which hampers performance. We proposed:

- **Efficient Training Algorithm**: Reduces computational overhead by leveraging efficient attention kernels and parallel processing.

- **Custom Noise Schedules**: Employing "clipped noise schedules" minimized the gradient variance effectively:

  > "We identify gradient variance as a limiting factor [...] and propose custom data-driven noise schedules that reduce gradient variance."

## Experimental Validation

Our empirical results highlight the significant performance improvements of BD3-LMs:

- **State-of-the-art Perplexity**: We achieved notable improvements in perplexity over previous diffusion models:

  > "Our results establish a new state-of-the-art perplexity for discrete diffusion and make progress toward closing the gap to autoregressive models."

- **Variable-length Sequence Generation**: BD3-LMs successfully generated longer sequences than prior discrete diffusion models:

  > "BD3-LMs generate sequences up to approximately 10× longer than [...] models restricted to the training context size."

## Analysis of Gradient Variance

An insightful finding from our work was linking performance gaps to gradient variance rather than inherent model capability:

> "Training under the discrete diffusion NELBO [...] has similar training variance to an AR model with a random batch size."

We showed through careful tuning of noise schedules that gradient variance was significantly reduced:

> "While training on the NELBO results in a variance of 1.52, training under full masking reduces the variance to 0.11."

## Conclusion

Our work, BD3-LMs, successfully addresses crucial limitations inherent in diffusion and autoregressive language modeling frameworks. We enable high-quality, variable-length sequence generation, significantly reducing the gap in likelihood modeling between diffusion and AR models. Our contributions set the foundation for further advances in flexible, efficient language modeling:

> "BD3-LMs are able to generate sequences of arbitrary length, including lengths that exceed their training context [...] and achieve new state-of-the-art perplexities among discrete diffusion models."

