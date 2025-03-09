# Small Language Models: Survey, Measurements, and Insights

- **Published Date**: 2024-09
- **Authors**: Zhenyan Lu, Xiang Li, Dongqi Cai, Rongjie Yi, Fangming Liu, Xiwen Zhang, Nicholas D. Lane, Mengwei Xu
- **Link**: https://arxiv.org/abs/2409.15790
- **Summary Date**: 2025-03-09
- **Summarized by**: @wooksong
- **Tool Used**: NotebookLM
- **Tags**: #SLM #survey #OnDevice #Quantization #Benchmarking

## 개요

본 보고서는 데이터 센터 및 클라우드 환경에 주로 배포되는 대형 언어 모델(LLM)에 비해 학문적 관심이 적었던 소형 언어 모델(SLM)에 대한 최초의 종합적인 조사를 제공합니다. 스마트 장치에 널리 채택되었음에도 불구하고 SLM 연구는 LLM 연구와 달리 접근성, 경제성, 효율성을 목표로 합니다. 본 보고서는 1억에서 50억 개의 파라미터를 갖는 트랜스포머 기반 디코더 전용 언어 모델 59개를 조사하여 아키텍처, 훈련 데이터셋, 훈련 알고리즘 측면에서의 기술 혁신을 분석합니다. 또한 상식 추론, 인컨텍스트 학습, 수학, 코딩 등 다양한 영역에서 SLM의 성능을 평가하고, 장치 내 추론 지연 시간 및 메모리 사용량 벤치마킹을 통해 런타임 비용에 대한 심층적인 통찰력을 제공하여 SLM 연구 발전에 기여하고자 합니다.

## 주요 내용 및 아이디어

### 1. 서론 및 SLM의 중요성

- 언어 모델의 발전은 인공 일반 지능(AGI) 추구를 위한 거대 LLM과 리소스 효율적인 배포를 위한 SLM으로 양분되고 있습니다. SLM은 데스크톱, 스마트폰, 웨어러블 기기 등에서 머신 인텔리전스를 민주화하여 언제 어디서든 누구나 접근하고 사용할 수 있도록 하는 비전을 제시합니다.

- 실제 세계에서 SLM은 이미 Google Gemini Nano, iOS 온디바이스 모델 등 상용 기기에 대규모로 통합되었음에도 불구하고 학계의 관심은 LLM에 비해 현저히 부족했습니다.

- 본 연구는 100M~5B 파라미터 범위의 디코더 전용 트랜스포머 아키텍처 SLM에 대한 최초의 포괄적인 조사로서, 성능, 런타임 비용, 기술 혁신을 심층적으로 분석하고, 향후 SLM 연구에 기여할 수 있는 귀중한 통찰력을 제공합니다.

    > "A vision for SLMs is to democratize machine intelligence, making it accessible and affordable to anyone, anywhere, at any time – just like the human brain that everyone possesses."

### 2. SLM 아키텍처, 데이터셋, 훈련

- **SLM 개요:** 2023년 말부터 SLM 모델 수가 급증하고 있으며, 본 연구에서는 GPT-2 아키텍처 기반의 디코더 전용 트랜스포머, 공개 가중치, 100M~5B 파라미터 범위의 59개 SLM을 선정하여 분석합니다.

  - 모델 아키텍처 분석:주요 구성 요소: 멀티 헤드 셀프 어텐션(MHA), 피드 포워드 신경망(FFN).

  - 시간에 따른 변화: MHA는 점차 그룹 쿼리 어텐션(GQA)으로 대체되고 있으며, 표준 FFN 대신 게이트 FFN이 널리 사용되는 추세입니다. FFN 중간 비율은 다양화되고 있으며, 활성화 함수는 ReLU에서 GELU 및 변형, 2024년에는 SiLU로 전환되는 경향을 보입니다. 레이어 정규화는 RMS 정규화로 대체되고 있으며, 어휘 크기는 점차 증가하여 최신 모델은 50K 이상의 어휘 크기를 갖습니다.

  - 가중치 공유: 임베딩-LM 헤드 가중치 공유는 일반적이며, MobiLLaMA, MobileLLM 등에서 레이어별 가중치 공유 기법이 사용됩니다. OpenELM은 레이어별 파라미터 스케일링을 통해 파라미터 활용도를 높입니다.

    > "As of August 2024, a typical SLM architecture tends to use group-query attention, gated FFN with SiLU activation, an intermediate ratio of FFN between 2 and 8, RMS normalization, and a vocabulary size larger than 50K."
  
  - 아키텍처 혁신의 영향은 런타임 성능에 상대적으로 더 큰 영향을 미치며, 바닐라 트랜스포머 대비 뚜렷한 우위를 보이는 혁신은 아직 검증이 필요합니다.

- **훈련 데이터셋:** The Pile이 가장 널리 사용되었으나 최근에는 RefinedWeb, RedPajama 등 다양한 고품질 데이터셋이 활용되는 추세입니다. SLM 성능 분석을 통해 데이터셋 품질을 비교한 결과, 고품질 데이터셋의 중요성이 강조됩니다.

  - 훈련 알고리즘:µP (Maximal Update Parameterization): Cerebras-GPT에서 훈련 안정성 및 하이퍼파라미터 전이성 향상을 위해 사용됩니다.

  - 지식 증류 (Knowledge Distillation): LaMini-GPT, Gemma-2에서 대형 모델의 지식을 소형 모델로 이전하는 데 활용됩니다.
  
  - 2단계 사전 훈련 전략: MiniCPM에서 대규모 저품질 데이터로 사전 훈련 후 고품질 데이터로 미세 조정을 수행합니다.

### 3. SLM 성능

- 평가 데이터셋 및 지표: 상식 추론, 문제 해결, 수학 영역의 12개 데이터셋을 사용하여 정확도를 주요 평가 지표로 사용합니다.

- 전반적인 성능: 2022년부터 2024년까지 모든 작업에서 상당한 성능 향상을 보였으며, 특히 Phi 모델은 최고 수준의 성능을 달성하여 LLaMA 3.1 7B 파라미터 모델과 유사한 수준을 보입니다. Qwen 2 1.5B 모델은 파라미터 수에 비해 뛰어난 성능을 보여주는 예외적인 경우입니다.

  > "Specifically, model performance improved by 10.4%, 13.5%, and 13.5% for the three tasks, respectively."

- 오픈 소스 데이터셋으로 훈련된 SLM은 상식 추론 작업에서 폐쇄 소스 모델과의 격차를 좁히고 있지만, 복잡한 추론 및 논리, 특히 수학 분야에서는 여전히 상당한 차이가 존재합니다.

- 인컨텍스트 학습 능력: 대부분의 SLM은 인컨텍스트 학습 능력을 보이며, 모델 크기가 클수록 그 능력이 향상되는 경향이 있습니다. 하지만 일부 간단한 데이터셋에서는 효과가 미미하거나 오히려 성능이 저하되는 경우도 있습니다. Gemma 2는 가장 큰 성능 향상을 보입니다.

### 4. SLM 런타임 비용

- 설정: Jetson Orin Module (GPU) 및 스마트폰 (CPU) 환경에서 llama.cpp 추론 엔진을 사용하여 20개 모델을 평가합니다. 4비트 양자화를 적용하여 실험합니다.

- 추론 지연 시간: 모델 크기에 따라 지연 시간이 증가하는 경향을 보이지만, 동일한 크기의 모델에서도 아키텍처에 따라 큰 차이를 보입니다. 프리필 단계는 연산량이 많아 컴퓨팅 바운드적인 반면, 디코드 단계는 메모리 바운드적인 특성을 가집니다.

- 메모리 사용량: 일반적으로 모델 파라미터 수에 따라 선형적으로 증가하지만, 어휘 크기가 큰 모델(예: Bloom)은 더 많은 메모리를 사용합니다. 긴 컨텍스트 길이를 지원하는 모델은 KV 캐시로 인해 더 많은 메모리를 소비합니다.

  > "Runtime memory usage is generally linearly correlated with the model’s parameter count."

- 양자화 및 하드웨어 영향

   - 양자화: 짧은 프롬프트에서는 양자화가 지연 시간을 크게 줄이지만, 프롬프트 길이가 길어지면 효과가 감소하는 경향이 있습니다. 디코드 단계에서는 양자화가 더 일관적인 성능 향상을 제공합니다. Q4 K M 방식이 가장 효과적인 것으로 나타났습니다.

  - 하드웨어: GPU (Jetson)는 CPU (Meizu 18 Pro)에 비해 프리필 단계에서 훨씬 빠른 속도를 보이며, 긴 추론 작업에서도 더 안정적인 성능을 유지합니다.
  
     > "GPU shows an even greater advantage over the CPU during the prefill phase."

  - 지연 시간 및 메모리 분석
    
    - 지연 시간 분석: 행렬-벡터 곱셈(mul mat vec q) 연산이 전체 추론 시간의 70% 이상을 차지합니다. Qwen2 모델은 넓은 FFN 레이어로 인해 해당 연산의 비중이 더 높습니다.

    - 메모리 분석: 모델 파라미터, KV 캐시, 연산 중 중간 변수가 주요 메모리 소비 요인입니다. 어휘 크기가 큰 모델은 더 큰 연산 버퍼를 필요로 합니다. 컨텍스트 길이가 길어지면 KV 캐시가 메모리 사용량의 대부분을 차지합니다.

    > "Mul mat vec (matrix by vector multiplication) is the most time-consuming operations of SLM, which constitute more than 70% end-to-end inference time."

### 5. 결론 및 향후 연구 방향

SLM은 성능과 효율성 측면에서 상당한 발전을 이루었으며, 향후 연구를 위한 다양한 가능성을 제시합니다.

- 향후 연구 방향: 
  - SLM 아키텍처와 장치 프로세서의 공동 설계 및 최적화 (Co-design and co-optimizations).
  - 배포 환경을 고려한 모델 스케일링 법칙 (Deployment-aware Chinchilla law).
  - 개인화를 위한 지속적인 온디바이스 학습 (Continual on-device learning for personalization).
  - SLM 성능의 공정한 벤치마킹 (Benchmarking SLMs fairly).
  - 희소 SLM 연구 (Sparse SLMs) 및 장치 내 외부 스토리지 활용.
  > "To push the limit of SLMs towards optimal accuracy-speed tradeoff, we advocate for extreme co-design and optimizations of SLM architecture with specific device hardware, possibly searching for a speed-optimal architectures before pre-training on them."
