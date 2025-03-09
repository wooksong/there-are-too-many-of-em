# Large Language Diffusion Models

- **Published Date**: 2025-02
- **Authors**: Shen Nie, Fengqi Zhu, Zebin You, Xiaolu Zhang, Jingyang Ou, Jun Hu, Jun Zhou, Yankai Lin, Ji-Rong Wen, Chongxuan Li
- **Link**: https://arxiv.org/abs/2502.09992
- **Summary Date**: 2025-03-09
- **Summarized by**: @wooksong
- **Tool Used**: NotebookLM
- **Tags**: #LLM, #LLaDA, #Diffusion, #Generative

## 개요

본 논문은 대규모 언어 모델(LLM)의 기반으로 널리 여겨지는 자기 회귀 모델(Autoregressive Models, ARMs)에 대한 도전으로, 마스크 확산 모델(Masked Diffusion Model, MDM)을 기반으로 한 새로운 LLM인 LLaDA(Large Language Diffusion with mAsking)를 소개합니다. LLaDA는 처음부터 사전 학습(Pre-training)과 지도 미세 조정(Supervised Fine-tuning, SFT) 패러다임 하에 훈련되었으며, 토큰 마스크 및 역방향 복원 과정을 통해 언어 분포를 모델링합니다. 본 논문은 LLaDA가 광범위한 벤치마크에서 강력한 확장성을 보여주며, 특히 컨텍스트 내 학습(In-context Learning)에서 강력한 LLM과 경쟁력을 갖추고, SFT 후에는 인상적인 명령어 추종 능력과 '역전 저주(Reversal Curse)' 해결 능력을 입증한다고 주장합니다.

## 주요 아이디어

### 자기 회귀 모델(ARM) 패러다임에 대한 도전
- 기존 LLM의 핵심 기술인 다음 토큰 예측 방식(ARM)이 LLM의 지능을 달성하는 유일한 방법인지에 대한 근본적인 질문을 제기합니다.

  > "Is the autoregressive paradigm the only viable path to achieving the intelligence exhibited by LLMs?"

### LLaDA: 마스크 확산 모델 기반 LLM

- LLaDA는 ARM 대신 MDM을 활용하여 양방향 의존성을 가진 모델 분포를 구축하고 로그 가능도 하한(Log-likelihood Lower Bound)을 최적화하는 새로운 접근 방식을 제시합니다.

  > "In contrast to traditional ARMs, LLaDA leverages a masked diffusion model (MDM)... This design enables LLaDA to construct a model distribution with bidirectional dependencies and optimize a lower bound of its log-likelihood, offering an unexplored and principled alternative to existing LLMs."

### LLaDA의 학습 및 구조

- 표준적인 데이터 준비, 사전 학습, SFT, 평가 파이프라인을 따릅니다.

- 80억(8B) 파라미터 크기로 확장되었으며, 2조 3천억(2.3T) 토큰으로 사전 학습되었습니다.

- 마스크 예측기로 바닐라 트랜스포머(Vanilla Transformer) 아키텍처를 사용하며, 순방향 마스크 프로세스와 역방향 예측 프로세스를 통해 학습합니다.

- ARM과 달리 인과적 마스크(Causal Mask)를 사용하지 않아 입력 전체를 볼 수 있습니다.

### LLaDA의 주요 성능

- 확장성 (Scalability): 동일한 데이터로 학습한 자체 구축 ARM 기반 모델과 비교했을 때 유사하거나 더 나은 성능을 보이며 강력한 확장성을 입증합니다. (그림 3 참
조)

  > "LLaDA scales effectively up to a computational budget of 10^23 FLOPs, achieving comparable results to self-constructed ARM baselines trained on the same data across six tasks, e.g., MMLU and GSM8K."

- 컨텍스트 내 학습 (In-Context Learning): 8B 모델은 LLaMA2 7B를 대부분의 제로/퓨샷 학습 작업에서 능가하며, LLaMA3 8B와 유사한 성능을 보입니다. (그림 1 참조)

  > "Remarkably, LLaDA 8B surpasses LLaMA2 7B... on nearly all 15 standard zero/few-shot learning tasks while performing on par with LLaMA3 8B..."

- 명령어 추종 (Instruction-Following): SFT 후 다중 턴 대화와 같은 사례 연구에서 향상된 명령어 추종 능력을 보여줍니다.
  
  > "LLaDA significantly enhances the ability to follow instructions after SFT, as demonstrated in case studies such as multi-turn dialogue."

- 역전 추론 (Reversal Reasoning): '역전 저주' 문제를 효과적으로 해결하며, 역방향 시 완성 작업에서 GPT-4o를 능가하는 성능을 보입니다. (표 3 참조)

  > "LLaDA effectively breaks the reversal curse... with consistent performance across forward and reversal tasks. Notably, it outperforms GPT-4o in a reversal poem completion task."

  - 이는 LLaDA가 토큰을 균일하게 처리하여 귀납적 편향(Inductive Bias)이 없기 때문으로 추정됩니다.

### 확률적 공식화 (Probabilistic Formulation)

- LLaDA는 순방향 마스크 프로세스와 역방향 복원 프로세스를 통해 모델 분포를 정의합니다.

- 손실 함수는 마스크된 토큰에 대해서만 계산되는 교차 엔트로피 손실이며, 이는 모델 분포의 음의 로그 가능도에 대한 상한(Upper Bound)임이 증명되었습니다.

  > "The core of LLaDA is a mask predictor, a parametric model pθ(·|xt) that takes xt as input and predicts all masked tokens (denoted M) simultaneously. It is trained using a cross-entropy loss computed only on the masked tokens..."

- LLaDA는 고정된 마스크 비율을 사용하는 마스크 언어 모델(Masked Language Models)과 달리 0에서 1 사이의 무작위 마스크 비율을 사용하며, 이는 더 나은 일반화와 컨텍스트 내 학습 능력에 기여할 수 있습니다.

### 추론 (Inference)

- 프롬프트가 주어지면 완전히 마스크된 응답에서 시작하여 역방향 프로세스를 시뮬레이션하여 텍스트를 생성합니다.

- 샘플링 단계 수는 효율성과 샘플 품질 간의 균형을 조절하는 하이퍼파라미터입니다.

- 낮은 신뢰도 재마스크(Low-confidence Remasking) 및 준-자기 회귀 재마스크(Semi-autoregressive Remasking)와 같은 재마스크 전략을 탐구하여 샘플링 품질을 향상시킵니다.

## 관련 연구

- 기존의 텍스트 생성을 위한 확산 모델 연구들을 언급하며, LLaDA가 대규모 언어 모델링을 위한 실용적인 확산 모델 접근 방식임을 강조합니다.

- 특히 마스크 확산 모델(MDM)의 최근 발전과 이론적 토대를 LLaDA 개발의 동기로 설명합니다.

## 결론 및 논의

- LLaDA는 확산 모델 기반의 대규모 언어 모델링에 대한 유망하고 새로운 접근 방식임을 강조합니다.

- ARM의 고유한 한계를 극복하고 새로운 가능성을 제시할 수 있음을 시사합니다.

- 향후 연구 방향으로 모델 크기 확장, 다중 모달 데이터 처리, 강화 학습 기반 정렬(Alignment), 추론 효율성 개선 등을 제시합니다.

  > "Our findings not only establish diffusion models as a viable and promising alternative but also challenge the prevailing assumption that these essential capabilities are inherently tied to ARMs."
