# Mixture of Scales: Memory-Efficient Token-Adaptive Binarization for Large Language Models

- **Published Date**: 2024-10
- **Authors**: Dongwon Jo, Taesu Kim, Yulhwa Kim, Jae-Joon Kim
- **Link**: https://arxiv.org/abs/2406.12311v1
- **Summary Date**: 2025-03-09
- **Summarized by**: @wooksong
- **Tool Used**: GPT-4o
- **Tags**: #LLM_Binarization, #MemoryEfficientAI, #Quantization, #TokenAdaptiveScaling, #ModelCompression

## **1. Introduction**

Binarization has been explored as an effective method for reducing the size of Large Language Models (LLMs), but conventional approaches significantly compromise their linguistic expressiveness. As the authors note,  

> "Binarization, which converts weight parameters to binary values, has emerged as an effective strategy to reduce the size of large language models (LLMs). However, typical binarization techniques significantly diminish the linguistic effectiveness of LLMs."

This challenge arises because reducing weights to a 1-bit representation dramatically restricts the capacity of neural networks to capture complex linguistic structures. To address this, **BinaryMoS (Mixture of Scales)** introduces a novel binarization method that uses multiple scaling factors dynamically selected for each token. The key innovation is that it **retains compression efficiency while improving model expressiveness**, even outperforming 2-bit quantization techniques.  

## **2. Understanding Binarization and Its Challenges**

Binarization works by converting floating-point weight values to 1-bit values, significantly reducing model size. The standard binarization equation is:  

$W_B = \alpha \cdot \text{Sign}(W_{FP} - \bar{W}_{FP})$

where $W_B$ represents the binarized weights, $W_{FP}$ denotes the full-precision weights, and $\alpha$ is the scaling factor that minimizes the L2 error between the two. Scaling factors are essential because they mitigate the loss of information introduced by binarization.  

The primary challenge with binarization is that **it drastically reduces the representational capacity of models, leading to performance degradation**. As the authors state:  

> "Such aggressive compression through binarization drastically limits the representational capacity of weights, leading to a significant degradation in the linguistic capabilities of LLMs."

Several previous approaches attempted to mitigate this issue. **PB-LLM** retains a subset of high-precision weights, but this introduces **memory overhead**. **BiLLM** refines this by introducing distinct scaling factors based on weight distributions but **adds structural complexity**. **OneBit** simplifies binarization by introducing **dual scaling factors for both input and output dimensions**, reducing error while maintaining efficiency. However, even these improved methods fail to fully close the accuracy gap with full-precision models.  

This leads to the fundamental research question:  

> *Can we improve the accuracy of binarized LLMs without compromising their memory efficiency?*  

## **3. BinaryMoS: A Token-Adaptive Approach to Binarization**  

BinaryMoS builds on the **Mixture of Experts (MoE) paradigm**, where multiple expert layers are typically used to enhance model capacity. However, instead of duplicating full layers (which is memory-intensive), BinaryMoS **applies the MoE concept to scaling factors**.  

Instead of a single static scaling factor, **BinaryMoS introduces multiple "scaling experts," which are dynamically weighted per token**. The process works as follows:  

First, a **router network** computes token-specific importance scores for each scaling expert:  

$G = \text{Softmax}(X W_R)$

where $W_R$ represents the router's learned weight parameters and $X$ the input activations.  

Next, **these scores determine the final scaling factors for the token**:  

$`\hat{S}_{in} = G S_{in}, \quad \hat{S}_{out} = G S_{out}`$

where $S_{\text{in}}$ and $S_{\text{out}}$ represent the expert scaling factors for input and output dimensions, respectively.  

During inference, these token-adaptive scaling factors replace static ones, allowing for a **context-aware adjustment of weight values**.  

As a result, BinaryMoS achieves **higher accuracy than previous binarization techniques while maintaining low memory overhead**.

## **4. Evaluating BinaryMoS: Performance and Compression Efficiency**

BinaryMoS is evaluated on multiple LLM architectures, including **OPT-125M, LLaMA-1-7B, and LLaMA-2-13B**, using **perplexity (lower is better) and zero-shot accuracy (higher is better)** as performance metrics.  

Key findings show that **BinaryMoS achieves compression ratios of 9.65× to 11.24×, closely matching OneBit while offering significantly improved accuracy**. The **perplexity values for LLaMA-1-7B** illustrate this improvement:

```
PB-LLM:     198.37 (WikiText2), 157.35 (C4)
OneBit:      8.48 (WikiText2), 10.49 (C4)
BinaryMoS:   7.97 (WikiText2), 9.72 (C4)   # Best performance among 1-bit methods
```

Moreover, **BinaryMoS outperforms 2-bit quantization methods like GPTQ and OmniQuant, despite requiring only half the memory**.  

> "BinaryMoS significantly reduces the memory footprint of models, achieving compression ratios ranging from 9.65× to 11.24×, enabling practical deployment on resource-constrained devices."

## **5. Why BinaryMoS Works: Theoretical and Experimental Insights**  

The effectiveness of BinaryMoS stems from its ability to **extend the representation space of binarized models through token-adaptive scaling factors**. The authors verify this claim by analyzing the **distribution of token-adaptive scaling factors**, showing that BinaryMoS **provides a much richer range of values than static binarization methods**.  

An **ablation study on the number of scaling experts** reveals that **four experts offer an optimal balance between accuracy and memory efficiency**. Increasing beyond four experts **introduces training difficulties in the router**, reducing performance.  

Furthermore, **latency analysis on NVIDIA A6000 GPUs confirms that BinaryMoS maintains inference speed comparable to OneBit**, despite its additional computations.  

## **6. Conclusion and Future Directions**  

BinaryMoS introduces a **paradigm shift in LLM binarization** by **leveraging Mixture of Experts to enhance token-adaptive scaling factors**. This approach:  

- **Maintains the memory efficiency of binarization** while significantly enhancing model accuracy.  
- **Bridges the gap between 1-bit binarized and full-precision models**.  
- **Surpasses 2-bit quantization methods** in both perplexity and zero-shot accuracy.  

However, challenges remain. **Scaling BinaryMoS to models like LLaMA-2-70B is computationally expensive**, necessitating exploration into **Post-Training Quantization (PTQ) adaptations**. Additionally, **further research on MoE-based routing optimizations** could enhance token assignments, potentially improving BinaryMoS.  

In conclusion, BinaryMoS **brings 1-bit LLMs closer to practical deployment** than ever before. As the field of **model compression and on-device AI evolves**, BinaryMoS stands as a benchmark for **memory-efficient, high-performance LLMs**.
