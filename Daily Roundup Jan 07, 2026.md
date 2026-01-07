---
share: true
---
# 논문 Daily Digest 분석 보고서

## 2026-01-06

-----

## 고득점 논문 (Stage 2 분석, 최종 >=5점)

-----

### [8점] CoCo-Fed: Communication and Computation Efficient Federated Learning at Wireless Edge

#### Metadata

- **arXiv**: 2601.00549
- **Link**: https://arxiv.org/abs/2601.00549
- **예측 점수**: 7점 → **최종 점수: 8점**

#### 4-Point Summary

|항목         |내용                                                                                                                 |
|-----------|-------------------------------------------------------------------------------------------------------------------|
|a) 해결하려는 문제|O-RAN gNB에서 local training의 memory footprint + backhaul bandwidth saturation                                       |
|b) 기존 방식 한계|LoRA는 frozen backbone 제약, GaLore는 communication overhead 미해결                                                       |
|c) 제안 방법   |Double-dimension down-projection (gradient low-rank) + orthogonal subspace superposition (layer-wise consolidation)|
|d) 효과      |LoRA/GaLore 대비 memory + communication efficiency 동시 개선, non-IID에서도 robust                                          |

#### Storage Relevance Score: 8/8

|기준            |점수 |근거                                                      |
|--------------|:-:|--------------------------------------------------------|
|Direct Storage|3/3|Optimizer state memory 직접 감소, gradient checkpoint 크기 감소 |
|Algorithmic   |3/3|Low-rank gradient projection + orthogonal subspace 합성   |
|System Impact |2/2|O-RAN edge gNB 실제 deployment 가능, backhaul-storage 동시 최적화|

#### Patent Ideas

1. **Gradient Rank-aware Storage Tiering**: Gradient의 intrinsic rank에 따라 SSD/HBM/DRAM 자동 배치
2. **Orthogonal Subspace Checkpoint**: Layer별 gradient를 orthogonal basis로 변환하여 압축 저장
3. **Backhaul-aware Gradient Buffer**: Network 상태에 따른 SSD-based gradient 버퍼링

#### Action Items

- [ ] Checkpoint size 영향 정량 분석 (GaLore baseline 대비)
- [ ] O-RAN 환경에서 SSD-based gradient buffer 평가
- [ ] Storage I/O 패턴 프로파일링 (GaLore vs CoCo-Fed)

-----

### [7점] Fast-weight Layers for Episodic Memory with Product Key Retrieval

#### Metadata

- **arXiv**: 2601.00671
- **Link**: https://arxiv.org/abs/2601.00671
- **예측 점수**: 6점 → **최종 점수: 7점**

#### 4-Point Summary

|항목         |내용                                                                                 |
|-----------|-----------------------------------------------------------------------------------|
|a) 해결하려는 문제|Sequence modeling에서 storage capacity vs computational efficiency trade-off         |
|b) 기존 방식 한계|Static PKM은 training/inference 분리, fine-tuning 필요                                  |
|c) 제안 방법   |Static PKM을 dynamic “fast-weight” episodic memory로 변환, chunk-level gradient descent|
|d) 효과      |4K token 학습으로 128K context 일반화 달성                                                  |

#### Storage Relevance Score: 7/8

|기준            |점수 |근거                                                            |
|--------------|:-:|--------------------------------------------------------------|
|Direct Storage|3/3|Memory 용량 vs 효율성 trade-off 직접 해결, PKM 저장 구조 제안                |
|Algorithmic   |3/3|Product Key Memory + fast-weight update + chunk-level gradient|
|System Impact |1/2|Long-context inference 지원, KV cache 대체 가능성                    |

#### Patent Ideas

1. **Tiered PKM Storage**: Hot/cold key-value pair 분리하여 HBM-SSD 계층적 저장
2. **Incremental PKM Checkpoint**: Fast-weight delta만 저장하는 incremental checkpoint
3. **PKM-aware Flash Management**: Key access pattern 기반 SSD wear-leveling

#### Action Items

- [ ] PKM access pattern 분석 (SSD wear-leveling 최적화)
- [ ] NVMe interface 최적화 평가
- [ ] KV cache 대체 가능성 검토

-----

### [6점] Memory Bank Compression for Continual Adaptation of Language Models

#### Metadata

- **arXiv**: 2601.00756
- **Link**: https://arxiv.org/abs/2601.00756
- **예측 점수**: 5점 → **최종 점수: 6점**

#### 4-Point Summary

|항목         |내용                                                           |
|-----------|-------------------------------------------------------------|
|a) 해결하려는 문제|Continual learning에서 memory bank가 무한히 커지는 scalability 문제     |
|b) 기존 방식 한계|기존 memory bank 방식은 task 수 증가에 따른 선형 증가 불가피                   |
|c) 제안 방법   |VQ-VAE 기반 codebook 최적화 + KV-LoRA + online resetting mechanism|
|d) 효과      |압축률 99.7% (0.3% 크기) 달성하면서 baseline 대비 정확도 유지                 |

#### Storage Relevance Score: 6/8

|기준            |점수 |근거                                              |
|--------------|:-:|------------------------------------------------|
|Direct Storage|2/3|Memory bank compression, 저장 공간 직접 감소            |
|Algorithmic   |3/3|VQ-VAE quantization + online resetting mechanism|
|System Impact |1/2|LLM inference에서 memory footprint 감소             |

#### Patent Ideas

1. **Hierarchical Memory Compression**: SSD-GPU-HBM 계층별 차등 압축률 적용
2. **Streaming Codebook Offload**: Memory bank codebook의 streaming 방식 offloading
3. **Memory Bank Aging Trigger**: 접근 빈도 기반 자동 압축 트리거

-----

### [6점] FlexSpec: Flexible Speculative Decoding with Edge-Cloud Collaboration

#### Metadata

- **arXiv**: 2601.00644
- **Link**: https://arxiv.org/abs/2601.00644
- **예측 점수**: 6점 → **최종 점수: 6점**

#### 4-Point Summary

|항목         |내용                                                                                    |
|-----------|--------------------------------------------------------------------------------------|
|a) 해결하려는 문제|Edge-cloud speculative decoding에서 model update storm + wireless latency               |
|b) 기존 방식 한계|Model 동기화마다 edge device 재배포 필요                                                        |
|c) 제안 방법   |Shared frozen anchor backbone으로 edge model 동기화 제거 + channel-aware adaptive speculation|
|d) 효과      |Edge model sync 제거, 32x A800 GPU 환경에서 검증                                              |

#### Storage Relevance Score: 6/8

|기준            |점수 |근거                                        |
|--------------|:-:|------------------------------------------|
|Direct Storage|2/3|Edge model storage 동기화 문제 해결              |
|Algorithmic   |2/3|Frozen anchor + adaptive speculation depth|
|System Impact |2/2|Edge-cloud 실제 deployment 문제 해결            |

#### Patent Ideas

1. **Version-agnostic Edge Model Cache**: 모델 버전 무관하게 동작하는 edge cache 설계
2. **Bandwidth-aware Model Sharding**: 네트워크 상태에 따른 동적 model shard 배치

-----

### [5점] QSLM: Quantization-aware Scaling for Spike-driven Language Models

#### Metadata

- **arXiv**: 2601.00679
- **Link**: https://arxiv.org/abs/2601.00679
- **예측 점수**: 5점 → **최종 점수: 5점**

#### 4-Point Summary

|항목         |내용                                                                             |
|-----------|-------------------------------------------------------------------------------|
|a) 해결하려는 문제|Spike-driven Language Model의 embedded 배포를 위한 memory footprint                  |
|b) 기존 방식 한계|기존 quantization은 SNN 특성 미고려, 정확도 손실 심각                                         |
|c) 제안 방법   |3-tier quantization search (global/block/module) + multi-objective optimization|
|d) 효과      |86.5% memory reduction, 20% power reduction, 84.4% accuracy on SST-2           |

#### Storage Relevance Score: 5/8

|기준            |점수 |근거                                   |
|--------------|:-:|-------------------------------------|
|Direct Storage|2/3|Model weight 저장 공간 86.5% 감소          |
|Algorithmic   |2/3|Tiered sensitivity-aware quantization|
|System Impact |1/2|Edge device deployment               |

#### Patent Ideas

1. **SNN-aware Storage Format**: Spike 패턴 고려한 모델 저장 포맷
2. **Tiered Model Loading**: Sensitivity 기반 순차적 layer loading

-----

### [5점] HFedMoE: Heterogeneous Federated Learning with Mixture-of-Experts

#### Metadata

- **arXiv**: 2601.00583
- **Link**: https://arxiv.org/abs/2601.00583
- **예측 점수**: 5점 → **최종 점수: 5점**

#### 4-Point Summary

|항목         |내용                                                                                               |
|-----------|-------------------------------------------------------------------------------------------------|
|a) 해결하려는 문제|MoE-based FL에서 heterogeneous resources + expert selection mismatch                               |
|b) 기존 방식 한계|기존 FL은 동질적 client 가정, MoE expert 선택 불일치                                                          |
|c) 제안 방법   |Expert importance identification + IB-based resource-aware selection + sparsity-aware aggregation|
|d) 효과      |DeepSeekMoE-16B fine-tuning 가능 (LLaMA-2 7B는 60GB GPU 필요)                                         |

#### Storage Relevance Score: 5/8

|기준            |점수 |근거                        |
|--------------|:-:|--------------------------|
|Direct Storage|2/3|Expert subset 저장/로딩 최적화   |
|Algorithmic   |2/3|Sparsity-aware aggregation|
|System Impact |1/2|Mobile device FL 가능       |

#### Patent Ideas

1. **Expert-aware Model Sharding**: Expert 중요도 기반 storage tier 배치
2. **Federated Expert Cache**: Client간 expert 공유 cache 설계

-----

## 검토 논문 (Stage 2 분석, 3-4점)

|#  |Title                                                       |arXiv     |예측→최종|주요 내용                                         |Link                                    |
|:-:|------------------------------------------------------------|----------|:---:|----------------------------------------------|----------------------------------------|
|1  |Splitting Precoding with Subspace Selection for Massive MIMO|2601.00616|4→4  |Fronthaul capacity 최적화, precoding matrix 압축 가능|[Link](https://arxiv.org/abs/2601.00616)|

-----

## 스킵 논문 (Stage 1에서 SKIP, 예측 <=3점)

### Medical/Bio (4편)

|Title                        |arXiv     |예측 점수|스킵 사유                    |
|-----------------------------|----------|:---:|-------------------------|
|Left Ventricle Segmentation  |2601.00794|1    |MRI 영상분석, storage 무관     |
|Pathology VLM Performance    |2601.00716|1    |의료 VLM 정확도, storage 무관   |
|Ependymoma Prognosis         |2601.00626|1    |의료 hypergraph, storage 무관|
|Parkinson’s Disease Profiling|2601.00519|1    |의료 multimodal, storage 무관|

### RL/Optimization Theory (7편)

|Title                                        |arXiv     |예측 점수|스킵 사유                          |
|---------------------------------------------|----------|:---:|-------------------------------|
|Spectral Signatures of Mathematical Reasoning|2601.00791|2    |이론 분석, storage 무관              |
|Categorical Reparameterization with Diffusion|2601.00781|2    |Diffusion 이론, storage 무관       |
|Reasoning-Creativity Trade-off               |2601.00747|1    |Cognitive 분석, storage 무관       |
|Stochastic Actor-Critic                      |2601.00737|2    |RL 알고리즘, storage 무관            |
|ARISE Swarm Exploration                      |2601.00693|1    |Multi-agent RL, storage 무관     |
|IRPO Reward Model RL                         |2601.00677|2    |Reward modeling, storage 무관    |
|Interpretability-Guided Optimization         |2601.00655|2    |Optimization theory, storage 무관|

### Domain-Specific Applications (23편)

|Category          |Count|Examples                 |
|------------------|:---:|-------------------------|
|Computer Vision   |2    |2601.00664, 2601.00617   |
|Robotics          |3    |Manipulation, Navigation |
|Autonomous Driving|2    |Perception, Planning     |
|Chemistry/Bio     |4    |Molecule, Protein        |
|Others            |12   |Sports, Retail, Finance 등|

-----

## 분석 통계

```
=== 2-Stage Analysis Summary ===

Stage 1 (Abstract Screening):
- 총 논문: 41편
- DEEP_ANALYSIS 대상: 7편 (예측 >=4점)
- SKIP: 34편 (예측 <=3점)

Stage 2 (PDF Deep Analysis):
- 분석 완료: 7편
- 필독 (>=5점): 6편
- 검토 (3-4점): 1편
- 스킵 (<=2점): 0편

효율성:
- PDF 다운로드 절감: 34편 스킵 (82.9%)
- 예측-최종 정확도: 1점 이내 100% (7/7)
```

-----

## 점수 분포

|점수 |논문 수|논문                   |
|:-:|:--:|---------------------|
|8  |1   |CoCo-Fed             |
|7  |1   |Fast-weight PKM      |
|6  |2   |Memory Bank, FlexSpec|
|5  |2   |QSLM, HFedMoE        |
|4  |1   |Splitting Precoding  |

-----

## 우선순위 특허 아이디어

|Priority|Paper               |Patent Direction                      |
|:------:|--------------------|--------------------------------------|
|1       |CoCo-Fed (8점)       |Gradient Rank-aware Storage Tiering   |
|2       |CoCo-Fed (8점)       |Orthogonal Subspace Checkpoint        |
|3       |Fast-weight PKM (7점)|Tiered PKM Storage (HBM-SSD hierarchy)|
|4       |Fast-weight PKM (7점)|PKM-aware Flash Management            |
|5       |Memory Bank (6점)    |Hierarchical Memory Compression       |

-----

## 첨부 파일

|파일                         |내용                  |
|---------------------------|--------------------|
|checkpoint_1_ids.txt       |41개 arXiv ID 목록     |
|checkpoint_2_metadata.json |논문 메타데이터            |
|checkpoint_3_screening.json|Stage 1 Screening 결과|
|deep_analysis_results.md   |Stage 2 상세 분석       |

-----

*Generated: 2026-01-06*
*Analyzer: storage-paper-analyzer skill*