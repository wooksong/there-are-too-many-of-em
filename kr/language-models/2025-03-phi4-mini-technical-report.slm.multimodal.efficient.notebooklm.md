# Phi-4-Mini Technical Report: Compact yet Powerful Multimodal Language Models via Mixture-of-LoRAs

- **Published Date**: 2025-03
- **Authors**: Microsoft Research
- **Link**: https://arxiv.org/abs/2503.01743
- **Summary Date**: 2025-03-09
- **Summarized by**: @wooksong
- **Tool Used**: NotebookLM
- **Tags**: #SLM, #MultiModal, #LoRA, #DataCentricLearning, #Reasoning

## 개요

본 문서는 마이크로소프트에서 발표한 Phi-4-Mini 및 Phi-4-Multimodal 모델에 대한 기술 보고서의 주요 내용과 핵심 아이디어를 검토합니다. Phi-4 모델은 소규모 파라미터에도 불구하고 뛰어난 성능을 보이는 언어 및 멀티모달 모델을 지향하며, 특히 고품질 데이터와 새로운 아키텍처 접근 방식을 통해 이를 달성합니다. 본 보고서는 Phi-4-Mini 언어 모델의 특징, Phi-4-Multimodal 모델의 혁신적인 멀티모달 통합 방식, 그리고 두 모델의 성능 및 안전성 평가 결과를 상세히 분석합니다.

## 주요 테마 및 핵심 아이디어

### 1. 소규모 모델의 강력한 성능

- Phi 모델 시리즈는 "세심하게 큐레이션되고 합성된 데이터"를 통해 소규모 언어 모델(SLM)이 "훨씬 더 큰 모델과 비교 가능한 결과"를 달성할 수 있음을 보여줍니다.

- Phi-4-Mini는 38억 개의 파라미터로 구성된 언어 모델임에도 불구하고, "고품질 웹 및 합성 데이터" 학습을 통해 유사한 크기의 최신 오픈소스 모델을 "상당히 능가"하며, "복잡한 추론을 요구하는 수학 및 코딩 작업에서 두 배 크기의 모델과 비슷한 성능"을 보입니다.

- 이러한 성과는 특히 "고품질 수학 및 코딩 데이터셋을 강조하는 신중하게 큐레이션된 합성 데이터 레시피"에 기인합니다.

### 2. Phi-4-Mini 언어 모델의 특징

- **확장된 어휘 크기:** 다국어 지원을 강화하기 위해 이전 모델인 Phi-3.5-Mini에 비해 "200K 토큰으로 확장된 어휘 크기"를 갖습니다.

- **효율적인 긴 시퀀스 생성:** 긴 문맥 생성을 위한 "그룹 쿼리 어텐션(Group Query Attention, GQA)" 메커니즘을 도입하여 KV 캐시 사용량을 최적화합니다. 모델은 "24개의 쿼리 헤드와 8개의 키/값 헤드"를 사용하여 KV 캐시 소비를 "표준 크기의 1/3"로 줄입니다.

- **향상된 문맥 처리:** "LongRoPE"를 기반으로 "128K 컨텍스트 길이"를 지원하며, "분수 RoPE 차원"을 사용하여 어텐션 헤드 차원의 25%가 위치에 불변하도록 설계하여 더 긴 문맥을 원활하게 처리합니다.

### 3. Phi-4-Multimodal 모델: 통합 멀티모달 지원

- Phi-4-Multimodal은 텍스트, 비전, 음성/오디오 입력 modality를 "단일 모델로 통합"한 멀티모달 모델입니다.

- **Mixture of LoRAs:** "LoRA 어댑터와 modality별 라우터"를 활용하는 새로운 modality 확장 방식을 통해 다양한 modality 조합을 간섭 없이 추론할 수 있도록 합니다. 예를 들어 "(비전 + 언어)", "(비전 + 음성)", "(음성/오디오)" 입력을 지원합니다.

- **뛰어난 음성 인식 성능:** 음성/오디오 modality의 LoRA 구성 요소가 "단 4억 6천만 개의 파라미터"만 가지고 있음에도 불구하고, "OpenASR 리더보드에서 현재까지 1위"를 차지합니다.

- **확장성:** 새로운 LoRA를 "기존 modality에 영향을 주지 않고" 원활하게 통합하여 추가 modality를 지원할 수 있는 "높은 확장성"을 가집니다.

### 4. 멀티모달 아키텍처 상세 정보

- **비전 modality:** "SigLIP-400M" 기반의 이미지 인코더와 텍스트 임베딩 차원으로 매핑하는 2층 MLP 프로젝터를 사용합니다.
  
  - LLM2CLIP으로 fine-tuning되었으며, 448x448 해상도를 지원합니다.
  
  - 언어 디코더의 모든 선형 레이어에 추가된 "LoRAV" 어댑터는 SFT 단계에서만 사용됩니다.
  
  - 비전 인코더 및 프로젝터는 "4억 4천만 개의 파라미터", LoRAV는 "3억 7천만 개의 파라미터"를 가집니다.

- **음성/오디오 modality:** "Conformer" 기반의 오디오 인코더와 3072 차원의 텍스트 임베딩 공간으로 매핑하는 2층 MLP 프로젝터를 사용합니다.
  
  - Phi-4-Mini의 모든 어텐션 및 MLP 레이어에 적용된 "rank 320의 LoRAA" 어댑터를 포함합니다.
  
  - 오디오 인코더 및 프로젝터는 "4억 6천만 개의 파라미터", LoRAA는 "4억 6천만 개의 파라미터"를 가집니다.
  
  - 음성 토큰 속도는 "80ms"로, 1분 오디오는 약 750개의 토큰으로 처리됩니다.

### 5. 학습 데이터 및 과정

**언어 모델:** 고품질의 추론 능력이 풍부한 텍스트 데이터와 엄선된 코딩 데이터셋을 사용하여 Phi-4-Mini를 학습합니다.

**멀티모달 모델:** 이미지-텍스트 쌍, 이미지 접지 데이터, OCR 및 차트 이해 합성 데이터 등 다양한 시각-텍스트 데이터를 포함하는 "0.5T 토큰" 규모의 데이터로 사전 학습합니다. 최대 이미지 해상도는 1344x1344입니다.

- 텍스트 SFT 데이터, 공개 및 내부 멀티모달 instruction tuning 데이터셋을 포함하는 "약 0.3T 토큰" 규모의 데이터로 supervised fine-tuning (SFT)을 진행합니다.

- 비전-음성 데이터는 비전-언어 SFT 데이터의 하위 집합과 내부 TTS 엔진을 사용하여 생성하며, WER 기반 필터링을 통해 품질을 관리합니다.

**추론 능력 향상 학습:** Phi-4-Mini를 기반으로 약 600억 개의 추론 CoT 토큰으로 사전 학습하고, 잘못된 출력을 필터링하기 위해 거부 샘플링을 사용합니다.

- 다양한 도메인과 난이도를 포괄하는 약 20만 개의 고품질 CoT 샘플로 fine-tuning합니다.

- 필터링된 잘못된 출력을 "비선호", 수정된 결과를 "선호"로 레이블링하여 30만 개의 선호도 샘플 데이터셋을 구축하고 DPO (Direct Preference Optimization) 학습을 진행합니다.

### 6. 성능 평가

- **비전-언어 벤치마크:** MMMU, ScienceQA, MathVista 등 다양한 공개 벤치마크에서 Phi-4-Multimodal은 유사하거나 더 큰 모델 대비 경쟁력 있는 성능을 보입니다. 특히 ShareGPT4o AI2D 및 ChartQA 벤치마크에서 InternOmni보다 "10점 이상 높은 성능"을 달성합니다.

- **비전-음성 벤치마크:** InternOmni에서 사용된 벤치마크를 통해 평가한 결과, Phi-4-Multimodal은 Gemini-2.0-Flash 및 GPT-4o와 비교하여 경쟁력 있는 성능을 보입니다.

- **음성 벤치마크:**
  - **ASR (Automatic Speech Recognition):** CV15, FLEURS, OpenASR 리더보드에서 WhisperV3, SeamlessM4T-V2, Qwen2-audio 등 다른 모델과 비교 평가됩니다. Phi-4-Multimodal은 언어 정보를 명시적으로 제공하지 않아도 대상 언어를 완벽하게 인식하는 능력을 보입니다.

  - **AST (Automatic Speech Translation):** CoVoST2 및 FLEURS 벤치마크에서 BLEU 점수로 평가됩니다. CoT (Chain-of-Thoughts) 추론을 통해 번역 품질이 크게 향상되며, CoVoST2 벤치마크에서 Gemini-2.0-Flash 및 GPT-4o를 포함한 평가 모델 중 가장 우수한 성능을 보입니다.

  - **SQQA (Spoken Query Question Answering):** MT-Bench 및 MMMLU 벤치마크에서 GPT-4-0613을 judge 모델로 사용하여 평가됩니다. Phi-4-Multimodal은 ASR 및 AST 작업에서 Gemini 및 GPT-4o를 능가하지만, SQQA 작업에서는 뒤처지는 경향이 있습니다. 이는 Phi-4-Multimodal이 음성 및 오디오 이해 작업에 더 최적화되어 있을 수 있기 때문입니다.

  - **Speech Summarization:** Golden3 및 AMI 벤치마크에서 GPT-4-0613을 judge 모델로 사용하여 평가됩니다.

  - **Audio Understanding:** AIR-Bench-chat 및 MMAU 벤치마크에서 GPT-4-0613을 judge 모델로 사용하여 평가됩니다. Phi-4-Multimodal은 오디오 인코더를 post-training에서 동결했음에도 불구하고, 평가된 벤치마크에서 강력한 음성, 오디오 및 음악 이해 능력을 보여주며, 오픈소스 모델인 Qwen2-audio를 능가합니다.

- **언어 벤치마크:** BigBench-Hard, MMLU, Arc-C, BoolQ, GPQA, HellaSwag, OpenBookQA, PIQA, SociQA, TruthfulQA, WinoGrande, Multilingual-MMLU, MGSM, GSM-8K, MATH 등 다양한 벤치마크에서 Llama 3, Qwen 2.5, Ministral, Gemma 등 유사하거나 더 큰 모델과 비교됩니다. Phi-4-Mini는 수학 및 추론 관련 벤치마크에서 뛰어난 성능을 보입니다.

- **코딩 벤치마크:** BigCodeBench, HumanEval, LCB, LiveBench, MBPP, Spider 등 다양한 벤치마크에서 평가됩니다. Phi-4-Mini는 코딩 관련 작업에서도 경쟁력 있는 성능을 나타냅니다.

### 7. 안전성 평가:

- **Responsible AI (RAI) 벤치마크:** 폭력, 성적 콘텐츠, 자해, 혐오 발언 등 4가지 유해 범주에 대해 Phi-4-Mini 및 Phi-4-Multimodal의 유해 콘텐츠 생성률(Defect Rate)을 평가합니다. 최신 Phi 모델은 이전 모델인 Phi-3.5-mini 및 유사한 크기의 다른 모델보다 "jailbreak에 더 강력"하며, jailbreak 존재를 감지하고 유해한 응답을 덜 생성하는 경향을 보입니다.

- **XSTest 프레임워크:** 유해한 프롬프트에 대한 거부율(IPRR)과 무해한 프롬프트에 대한 거부율(VPRR)을 측정합니다. Phi-4-Mini 및 Phi-4-Multimodal 모두 유해한 프롬프트에 대해 "매우 높은 거부율"을 보입니다. Phi-4-Multimodal은 무해한 프롬프트에 대해 다소 높은 거부율을 나타냅니다.

- **다국어 안전성 평가:** Azure AI Evaluation SDK를 활용하여 Tier 1 언어 전반에 걸쳐 유해 콘텐츠 생성률을 평가한 결과, Phi-4-Mini 및 Phi-4-Multimodal 모두 Phi-3.5-mini에 비해 개선된 안전성을 보입니다.

- **음성 입력 안전성:** 오디오 트레이스를 입력 프롬프트로 사용하여 RAI 벤치마크를 수행한 결과, Phi-4-Multimodal은 GPT-4o에 비해 경쟁력 있는 안전성을 보입니다.

- **음성 공정성:** 다양한 성별 및 연령 하위 그룹에 걸쳐 음성-텍스트 변환(STT) 성능의 공정성을 평가한 결과, 전반적인 성능 대비 현저하게 낮은 성능을 보이는 하위 그룹은 발견되지 않았습니다.

- **민감한 속성 추론 방지:** 사용자의 음성으로부터 민감한 속성(인종, 성적 지향, 정치적 성향 등 12가지)을 추론하는지 평가한 결과, 모델은 이를 추론하지 않는 것으로 나타났습니다.

- **비전 안전성:** Azure AI Evaluation SDK, 내부 벤치마크, 공개 RTVLM 및 VLGuard 벤치마크를 사용하여 텍스트와 이미지를 함께 입력했을 때의 안전성을 평가합니다. Phi-4-Multimodal은 Phi-3.5-Vision 및 기타 오픈소스 모델과 비교하여 경쟁력 있는 비전 안전성 지표를 보입니다.

## 결론

Phi-4-Mini 및 Phi-4-Multimodal 모델은 소규모 파라미터에도 불구하고 고품질 데이터와 혁신적인 아키텍처를 통해 언어 이해, 추론, 코딩, 그리고 멀티모달 작업에서 뛰어난 성능을 입증했습니다. 특히 Phi-4-Multimodal은 통합된 멀티모달리티 지원과 뛰어난 음성 처리 능력을 보여주며, 안전성 측면에서도 이전 모델 및 경쟁 모델 대비 개선된 결과를 나타냅니다. 이러한 성과는 소규모 모델이 다양한 실제 애플리케이션에서 강력한 성능을 발휘할 수 있는 가능성을 시사합니다.

## 향후 고려 사항

- 추론 능력 강화 버전의 Phi-4-Mini의 성능 및 출시 계획에 대한 추가 정보 필요.

- 다양한 실제 시나리오에서의 모델 성능 검증 및 개선 노력 지속 필요.

- 멀티모달 모델의 다양한 입력 조합 및 작업에 대한 추가적인 평가 및 분석 필요.

- 모델의 안전성 및 잠재적 위험에 대한 지속적인 모니터링 및 완화 방안 마련 필요.
