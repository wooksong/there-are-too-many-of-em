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

## Introduction and Problem Statement

Diffusion language models offer significant advantages such as parallel generation and output controllability, but they typically suffer from limitations like poor likelihood modeling performance, lack of variable-length generation, and inefficient inference. The paper introduces Block Discrete Denoising Diffusion Language Models (BD3-LMs), a novel approach to combining the best aspects of discrete diffusion and autoregressive (AR) models. This approach enables generating high-quality sequences of arbitrary length and significantly reduces the computational complexity associated with standard discrete diffusion models.

## Background Concepts

### Autoregressive (AR) Models

Autoregressive models define the probability of a sequence of tokens as a product of conditional probabilities:
\[
\log p_\theta(x) = \sum_{\ell=1}^{L}\log p_\theta(x^\ell|x^{<\ell})
\]
Each token's probability is directly parameterized by a neural network, making AR models efficient at modeling likelihood, although sequential token dependencies lead to slow inference speeds.

### Discrete Denoising Diffusion Probabilistic Models (D3PM)

Discrete diffusion models generate tokens through a Markovian forward and reverse diffusion process. Formally, this process gradually corrupts the input sequence by introducing noise. Given tokens \( x_\ell \) and noise levels \( t \), the forward diffusion step is defined as:
\[
q(x^\ell_t|x^\ell_s)=\text{Cat}(x^\ell_t; Q_t x^\ell_s)
\]
where \( Q_t \) is a diffusion matrix. The reverse process tries to reconstruct original tokens from corrupted data, defined by:
\[
p_\theta(x_s|x_t)=\prod_{\ell=1}^{L}\sum_x q(x^\ell_s|x^\ell_t,x^\ell)p_\theta(x^\ell|x_t)
\]
The training uses Negative ELBO (NELBO) optimization, minimizing the divergence between forward and reverse processes.

## Proposed Block Diffusion Method

### Block Diffusion Model Structure

The block diffusion model breaks the sequence into blocks, allowing sequential generation across blocks and parallel token generation within blocks. The conditional probability of a block given previous blocks utilizes discrete diffusion:
\[
\log p_\theta(x) = \sum_{b=1}^{B}\log p_\theta(x^b|x^{<b})
\]
Each conditional block is modeled as:
\[
p_\theta(x^b_s|x^b_t,x^{<b})=\sum_{x^b} q(x^b_s|x^b_t,x^b)p_\theta(x^b|x^b_t,x^{<b})
\]

### Efficient Training and Sampling

To address inefficiencies, the authors propose specialized algorithms:

- **Training Algorithm:**
  - Compute the key-value cache first, followed by denoised predictions for each block in parallel, improving computational efficiency.

- **Vectorized Training:**
  - Combine clean and noisy data into a single forward pass using a specialized attention mask, significantly reducing the training overhead.

- **Sampling Algorithm:**
  - Generate blocks sequentially but tokens within blocks simultaneously, utilizing precomputed keys and values, achieving both variable-length generation and inference efficiency.

## Analysis of Gradient Variance

The authors provide an insightful analysis into why discrete diffusion models underperform autoregressive models despite similar theoretical foundations. They identify gradient variance as the primary cause, demonstrating that:

- Diffusion models inherently use fewer tokens for gradient estimation (only masked tokens), increasing gradient variance.
- By adjusting the masking noise schedules (specifically, fully masking tokens), gradient variance significantly reduces, closing the perplexity gap between diffusion and AR models.

## Low-Variance Noise Schedules

### Clipped Schedules

To further reduce training variance, the authors introduce "clipped" noise schedules that limit mask probabilities to a beneficial range. Formally, mask rates \(1-\alpha_t\) are sampled uniformly within a specified range, enhancing learning signals and decreasing variance.

### Data-driven Optimization

The optimal mask range varies based on block size; thus, the authors propose an adaptive strategy optimizing noise schedule parameters during training by minimizing the estimated gradient variance. They demonstrate a clear correlation between lower variance schedules and improved perplexity across different block sizes.

## Experimental Results

- **Likelihood Performance:**
  - BD3-LMs consistently outperform previous diffusion methods across standard benchmarks, achieving state-of-the-art perplexities and closing the performance gap with autoregressive models.

- **Variable-Length Generation:**
  - The model effectively generates sequences significantly longer than standard diffusion models, demonstrating practical value in tasks requiring extended context.

- **Sampling Quality:**
  - Quantitative evaluations using generative perplexity further validate the superior quality of BD3-LMs outputs compared to baseline diffusion models.

## Ablations and Practical Insights

- The authors validate that the choice of noise schedule critically impacts performance, with optimized schedules greatly improving training stability and efficiency.
- Efficient training algorithms, leveraging specialized attention kernels, yield substantial computational savings, crucial for practical adoption.

## Conclusion and Contributions

The authors conclusively demonstrate that BD3-LMs successfully merge the advantages of autoregressive and diffusion models, setting new standards for discrete diffusion model performance. The significant contributions include:

- Introducing block diffusion modeling to support variable-length, high-quality sequence generation.
- Developing efficient training and inference algorithms reducing computational overhead.
- Identifying and addressing gradient variance as a critical barrier to diffusion model performance.
- Establishing new state-of-the-art benchmarks in discrete diffusion language modeling.
