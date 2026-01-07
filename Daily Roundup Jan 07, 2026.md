---
share: true
---
# 논문 Daily 분석 보고서 - 2026-01-06

> 수집: 343편 -> 키워드 필터: 240편 -> Abstract 스크리닝: 227편 -> 심층 분석: 20편

-----

## 메타데이터 (Claude 연동용)

```json
{
  "date": "2026-01-06",
  "total_collected": 343,
  "keyword_filtered": 240,
  "abstract_screened": 227,
  "deep_analysis_count": 20,
  "skip_count": 207,
  "high_score_papers": 8,
  "review_papers": 12,
  "analysis_method": "2-stage: abstract screening + PDF deep analysis"
}
```

-----

## 고득점 논문 (Stage 2 분석, 최종 6점 이상)

-----

### [7점] AGNES: Accelerating Storage-Based Training for Graph Neural Networks

#### Metadata

- **Title**: Accelerating Storage-Based Training for Graph Neural Networks
- **Authors**: Bigdasgit Team
- **Venue**: KDD ’26
- **Link**: https://arxiv.org/abs/2601.01473
- **Code**: https://github.com/Bigdasgit/agnes-kdd26
- **예측 점수**: 7점 -> **최종 점수**: 7점

#### 4-Point Summary

|항목         |내용                                                      |
|-----------|--------------------------------------------------------|
|a) 해결하려는 문제|대규모 그래프가 GPU/메모리 용량 초과 시 SSD 기반 GNN 학습 필요               |
|b) 기존 방식 한계|Power-law 분포로 인해 small random I/O 다수 발생, GPU 활용률 19%로 저하|
|c) 제안 방법   |Block-wise 저장 및 Hyperbatch 처리로 연속적 I/O 생성, 블록 재사용 극대화   |
|d) 효과      |Ginex/GNNDrive 대비 4.1x 속도 향상, I/O 대역폭 활용 4.5x 개선        |

#### Storage Relevance Score: 7/8

|기준            |점수 |근거                                         |
|--------------|:-:|-------------------------------------------|
|Direct Storage|3/3|NVMe SSD 직접 활용, I/O 패턴 최적화가 핵심 contribution|
|Algorithmic   |2/3|Hyperbatch로 접근 패턴 변경, 블록 재사용 스케줄링          |
|System Impact |2/2|대규모 GNN 학습의 스토리지 병목 해결, TB급 그래프 처리 가능      |

#### Patent Ideas

1. Power-law 그래프에서 공동 접근 노드의 연속 ID 할당 방법
2. Multi-minibatch 블록 재사용 스케줄링 시스템
3. 그래프 구조 기반 SSD I/O 최적화 저장 레이아웃

#### Action Items

- [ ] Section 4.2 Block-wise Storage Layout 상세 분석
- [ ] MAG240M 데이터셋 I/O 패턴 벤치마크 검토
- [ ] 특허 출원 검토: 그래프 I/O 최적화 방법

-----

### [6점] RelayGR: Scaling Long-Sequence Generative Recommendation

#### Metadata

- **Title**: RelayGR: Scaling Long-Sequence Generative Recommendation via Cross-Stage Relay-Race Inference
- **Authors**: Huawei Technologies
- **Venue**: Industry Track
- **Link**: https://arxiv.org/abs/2601.01712
- **예측 점수**: 6점 -> **최종 점수**: 6점

#### 4-Point Summary

|항목         |내용                                                   |
|-----------|-----------------------------------------------------|
|a) 해결하려는 문제|추천 시스템의 긴 사용자 행동 시퀀스 처리 시 tail-latency SLO 위반        |
|b) 기존 방식 한계|단일 단계에서 전체 시퀀스 처리 시 지연 시간 급증, 자원 낭비                  |
|c) 제안 방법   |장기 행동을 prefix로 pre-infer하여 KV 캐시 생성, ranking 단계에서 재사용|
|d) 효과      |동일 SLO 하에서 1.5x 더 긴 시퀀스 지원, 3.6x throughput 향상       |

#### Storage Relevance Score: 6/8

|기준            |점수 |근거                                    |
|--------------|:-:|--------------------------------------|
|Direct Storage|2/3|KV 캐시 관리, HBM/DRAM 계층화 저장 전략          |
|Algorithmic   |2/3|Cross-stage 캐시 재사용, Affinity-aware 라우팅|
|System Impact |2/2|대규모 추천 시스템 인프라에 직접 영향                 |

#### Patent Ideas

1. 파이프라인 단계 간 KV 캐시 affinity 라우팅 시스템
2. 요청 수명주기 기반 HBM-DRAM 계층적 캐시 관리 방법

-----

### [6점] TARRAGON: Making MoE based LLM Inference Resilient

#### Metadata

- **Title**: Making MoE based LLM inference resilient with Tarragon
- **Authors**: UC Riverside, Cisco Research
- **Venue**: Systems Track
- **Link**: https://arxiv.org/abs/2601.01310
- **예측 점수**: 6점 -> **최종 점수**: 6점

#### 4-Point Summary

|항목         |내용                                                       |
|-----------|---------------------------------------------------------|
|a) 해결하려는 문제|MoE 모델의 수백 GPU 분산 추론에서 노드 장애 시 전체 작업 재시작                 |
|b) 기존 방식 한계|단일 노드 장애가 전체 파이프라인 중단 유발, 복구 시간 과다                       |
|c) 제안 방법   |Attention Workers와 Expert Workers 분리 장애 도메인, 비동기 KV 체크포인팅|
|d) 효과      |장애 유발 정지 160-213x 감소, 복구 지연 1800x 감소                     |

#### Storage Relevance Score: 6/8

|기준            |점수 |근거                                    |
|--------------|:-:|--------------------------------------|
|Direct Storage|1/3|KV 캐시 체크포인팅을 위한 저장소 활용                |
|Algorithmic   |3/3|장애 도메인 분리, incremental checkpoint 알고리즘|
|System Impact |2/2|대규모 MoE 추론 안정성 및 가용성 향상               |

#### Patent Ideas

1. MoE 추론에서 Attention/Expert 분리 장애 복구 시스템
2. 비동기 incremental KV 캐시 체크포인팅 방법

-----

### [6점] Placement Semantics for Distributed Deep Learning

#### Metadata

- **Title**: Placement Semantics for Distributed Deep Learning
- **Authors**: Deep Mehta (Adobe Inc.)
- **Venue**: Adobe Research
- **Link**: https://arxiv.org/abs/2601.02311
- **예측 점수**: 6점 -> **최종 점수**: 6점

#### 4-Point Summary

|항목         |내용                                                  |
|-----------|----------------------------------------------------|
|a) 해결하려는 문제|분산 학습의 병렬화 전략(ZeRO, FSDP, TP, PP)을 통합 분석하는 프레임워크 부재 |
|b) 기존 방식 한계|각 전략이 독립적으로 설계되어 비교/조합 분석 어려움                       |
|c) 제안 방법   |4가지 학습 상태와 5가지 배치 모드로 형식화, 메모리/통신 자동 유도             |
|d) 효과      |ZeRO Stage 2->3: 배치 하나 변경으로 메모리 8x 감소, 통신 1.5x 증가 예측|

#### Storage Relevance Score: 6/8

|기준            |점수 |근거                             |
|--------------|:-:|-------------------------------|
|Direct Storage|2/3|Offloaded 모드 포함, 메모리-스토리지 계층 분석|
|Algorithmic   |2/3|병렬화 전략의 형식적 분석 및 자동 유도         |
|System Impact |2/2|모든 주요 병렬화 기법 통합, 시스템 설계 지원     |

#### Patent Ideas

1. 배치 명세 기반 분산 학습 메모리/통신 자동 예측 시스템

-----

### [6점] Warp-Cortex: Memory-Efficient Architecture for Million-Agent

#### Metadata

- **Title**: Warp-Cortex: Memory-Efficient Architecture for Million-Agent Cognitive Scaling
- **Authors**: JorgeLRW
- **Venue**: arXiv preprint
- **Link**: https://arxiv.org/abs/2601.01298
- **Code**: https://github.com/JorgeLRW/warp-cortex
- **예측 점수**: 6점 -> **최종 점수**: 6점

#### 4-Point Summary

|항목         |내용                                                  |
|-----------|----------------------------------------------------|
|a) 해결하려는 문제|Multi-agent LLM 프레임워크의 선형 메모리 확장 (10개 7B 모델 = 140GB)|
|b) 기존 방식 한계|에이전트 수에 비례하여 메모리 증가, consumer hardware에서 불가능        |
|c) 제안 방법   |단일 모델 공유(Prism) + TDA 기반 랜드마크 KV 캐시(Synapse)        |
|d) 효과      |RTX 4090에서 100개 에이전트를 2.2GB VRAM으로 실행, O(1) 가중치 메모리 |

#### Storage Relevance Score: 6/8

|기준            |점수 |근거                                |
|--------------|:-:|----------------------------------|
|Direct Storage|2/3|KV 캐시 메모리 32k당 1GB->10MB로 극적 감소   |
|Algorithmic   |2/3|TDA 기반 랜드마크 선택 알고리즘               |
|System Impact |2/2|Consumer hardware에서 대규모 에이전트 실행 가능|

#### Patent Ideas

1. Topological Data Analysis 기반 KV 캐시 압축 방법
2. Multi-agent 가중치 공유 아키텍처

-----

### [6점] MCGI: Manifold-Consistent Graph Indexing for Disk-Resident Vector Search

#### Metadata

- **Title**: MCGI: Manifold-Consistent Graph Indexing for Disk-Resident Vector Search
- **Authors**: Dongfang Zhao (University of Washington)
- **Venue**: arXiv preprint
- **Link**: https://arxiv.org/abs/2601.01930
- **예측 점수**: 4점 -> **최종 점수**: 6점

#### 4-Point Summary

|항목         |내용                                                         |
|-----------|-----------------------------------------------------------|
|a) 해결하려는 문제|고차원 벡터 검색에서 Euclidean-Geodesic 불일치로 그래프 라우팅 성능 저하          |
|b) 기존 방식 한계|DiskANN 등 기존 방식이 매니폴드 구조 무시, 불필요한 I/O 발생                   |
|c) 제안 방법   |LID로 매니폴드 곡률 추정, 곡률 높은 영역에서 검색 예산 동적 조절                    |
|d) 효과      |GIST1M에서 DiskANN 대비 5.8x throughput, SIFT1B에서 3x latency 감소|

#### Storage Relevance Score: 6/8

|기준            |점수 |근거                            |
|--------------|:-:|------------------------------|
|Direct Storage|2/3|Disk-resident 인덱스, I/O 최적화가 핵심|
|Algorithmic   |2/3|매니폴드 인식 적응형 검색 알고리즘           |
|System Impact |2/2|대규모 벡터 검색 시스템 실용화             |

#### Patent Ideas

1. LID 기반 적응형 빔 서치 방법
2. 매니폴드 곡률 인식 그래프 인덱싱 구조

-----

### [6점] SafeLoad: Efficient Admission Control for Memory-Overloading Queries

#### Metadata

- **Title**: SafeLoad: Efficient Admission Control Framework for Identifying Memory-Overloading Queries in Cloud Data Warehouses
- **Authors**: Zhejiang University, Alibaba Cloud
- **Venue**: VLDB ’25
- **Link**: https://arxiv.org/abs/2601.01888
- **Code**: https://github.com/SafeLoad-project/SafeBench
- **예측 점수**: 4점 -> **최종 점수**: 6점

#### 4-Point Summary

|항목         |내용                                                   |
|-----------|-----------------------------------------------------|
|a) 해결하려는 문제|클라우드 데이터웨어하우스에서 메모리 초과 쿼리로 인한 실행 실패 및 자원 낭비          |
|b) 기존 방식 한계|기존 admission control이 MO 쿼리 식별 precision 0.52로 낮음    |
|c) 제안 방법   |Discriminative rule + 하이브리드 모델 + self-tuning quota 관리|
|d) 효과      |최고 baseline 대비 precision 66% 향상, CPU 낭비 8.09x 감소     |

#### Storage Relevance Score: 6/8

|기준            |점수 |근거                      |
|--------------|:-:|------------------------|
|Direct Storage|2/3|메모리 자원 관리, 서버리스 클러스터 라우팅|
|Algorithmic   |2/3|ML 기반 쿼리 메모리 예측 및 분류    |
|System Impact |2/2|대규모 클라우드 DB 안정성 및 자원 효율성|

#### Patent Ideas

1. 쿼리 메모리 요구량 예측 기반 admission control 시스템

-----

### [6점] FLAMENCO: Low Latency Multiprogramming Quantum Computing Architecture

#### Metadata

- **Title**: A System Architecture for Low Latency Multiprogramming Quantum Computing
- **Authors**: ICT CAS, Southeast University
- **Venue**: arXiv preprint
- **Link**: https://arxiv.org/abs/2601.01158
- **예측 점수**: 4점 -> **최종 점수**: 6점

#### 4-Point Summary

|항목         |내용                                                    |
|-----------|------------------------------------------------------|
|a) 해결하려는 문제|양자 컴퓨팅에서 온라인 컴파일이 런타임의 대부분 차지                         |
|b) 기존 방식 한계|MPQC가 매번 co-optimization 필요, 실시간 서비스에 부적합             |
|c) 제안 방법   |오프라인 multi-version 컴파일 + 런타임 fidelity 기반 orchestration|
|d) 효과      |온라인 컴파일 제거로 5x 런타임 속도 향상, fidelity 개선                 |

#### Storage Relevance Score: 6/8

|기준            |점수 |근거                           |
|--------------|:-:|-----------------------------|
|Direct Storage|2/3|실행파일 버전 관리, 오프라인 컴파일 결과 저장   |
|Algorithmic   |2/3|Multi-version 컴파일 및 런타임 선택 전략|
|System Impact |2/2|양자-AI 서비스 실용화, QNN 반복 호출 지원  |

#### Patent Ideas

1. 양자 프로그램 multi-version 오프라인 컴파일 시스템

-----

## 검토 논문 (Stage 2 분석, 3-5점)

|# |Title                   |arXiv     |예측->최종|주요 내용                                  |Link                                    |
|--|------------------------|----------|------|---------------------------------------|----------------------------------------|
|1 |Heterogeneous SparseLoCo|2601.02360|5->5  |저대역폭 학습, 활성화 압축, subspace 기반           |[Link](https://arxiv.org/abs/2601.02360)|
|2 |HFRWKV Accelerator      |2601.02135|5->5  |FPGA 기반 RWKV, 171.36x energy efficiency|[Link](https://arxiv.org/abs/2601.02135)|
|3 |OrchestrRL              |2601.01209|5->5  |분산 RL 오케스트레이션, 광-전기 fabric             |[Link](https://arxiv.org/abs/2601.01209)|
|4 |DiT-HC                  |2601.01500|5->5  |HPC CPU 클러스터 DiT 학습, 90.6% weak scaling|[Link](https://arxiv.org/abs/2601.01500)|
|5 |T3C                     |2601.01299|5->5  |테스트타임 텐서 압축, 예산 조절 가능                  |[Link](https://arxiv.org/abs/2601.01299)|
|6 |FFCz                    |2601.01596|4->4  |스펙트럼 보존 손실 압축, 과학 데이터                  |[Link](https://arxiv.org/abs/2601.01596)|
|7 |Accelerated FWI         |2601.01268|4->4  |지진 데이터 압축 학습, TB급 데이터 처리               |[Link](https://arxiv.org/abs/2601.01268)|
|8 |EverMemOS               |2601.02163|4->4  |메모리 OS, engram 기반 장기 추론                |[Link](https://arxiv.org/abs/2601.02163)|
|9 |SWH                     |2601.01313|4->4  |시퀀스 모델링 O(T log T), FFT 기반             |[Link](https://arxiv.org/abs/2601.01313)|
|10|Hidden Costs            |2601.01698|4->4  |임베디드 추론, MAC vs 실제 시간 불일치              |[Link](https://arxiv.org/abs/2601.01698)|
|11|CounterPoint            |2601.01265|4->4  |HW 이벤트 카운터, 마이크로아키텍처 분석                |[Link](https://arxiv.org/abs/2601.01265)|
|12|SKA MOEA                |2601.01980|4->4  |천문 데이터 파이프라인, 스토리지 병목                  |[Link](https://arxiv.org/abs/2601.01980)|

-----

## 스킵 논문 (Stage 1에서 SKIP, 예측 3점 이하)

총 207편이 Stage 1 Abstract Screening에서 스킵되었습니다.

|분류              |편수|스킵 사유                   |
|----------------|--|------------------------|
|Vision/Image 전용 |52|Storage 관련성 없음, 순수 CV 연구|
|NLP/Language 전용 |48|모델 학습/추론 언급 없음, 언어 분석 중심|
|RL/Robotics     |31|제어/동작 계획 중심, 스토리지 무관    |
|Theory/Math     |28|이론적 분석, 시스템 구현 없음       |
|Medical/Bio     |25|도메인 특화, 스토리지 시사점 없음     |
|Security/Privacy|15|암호화/공격 방어, 스토리지 무관      |
|Others          |8 |기타 무관 분야                |

상세 목록은 checkpoint_3_screening.json의 status: “SKIP” 항목 참조.

-----

## 2-Stage Analysis Summary

```
=== 2-Stage Analysis Summary ===

Stage 1 (Abstract Screening):
- 총 논문: 227편
- DEEP_ANALYSIS 대상: 20편 (예측 4점 이상)
- SKIP: 207편 (예측 3점 이하)

Stage 2 (PDF Deep Analysis):
- 분석 완료: 20편
- 필독 (6점 이상): 8편
- 검토 (4-5점): 12편
- 스킵 (3점 이하): 0편

효율성:
- PDF 다운로드 절감: 207편 스킵 (Stage 1에서 필터링)
- 정확도: 예측-최종 일치율 85% (17/20편)
- 상향 조정: 3편 (MCGI, SafeLoad, FLAMENCO: 4점->6점)
```

-----

## 주요 트렌드 분석

### 1. KV 캐시 최적화 (3편)

- RelayGR: Cross-stage KV 캐시 재사용
- TARRAGON: KV 캐시 incremental 체크포인팅
- Warp-Cortex: TDA 기반 KV 캐시 압축

시사점: KV 캐시는 LLM 추론의 핵심 병목. 계층적 저장, 선택적 보존, 체크포인팅 기법이 활발히 연구됨.

### 2. 대규모 분산 학습/추론 (4편)

- Placement Semantics: 병렬화 전략 형식화
- TARRAGON: MoE 장애 복원력
- OrchestrRL: RL 파이프라인 오케스트레이션
- DiT-HC: CPU 클러스터 활용

시사점: 수백-수천 GPU 환경에서 통신/메모리 최적화, 장애 복구가 핵심 과제.

### 3. Storage-Compute 통합 (2편)

- AGNES: NVMe SSD 기반 GNN 학습
- MCGI: Disk-resident 벡터 인덱싱

시사점: GPU/CPU 메모리 한계 극복을 위한 SSD 직접 활용 증가.

-----

## 특허 아이디어 후보 (Top 5)

### 1. Power-law 그래프 블록 최적화 (AGNES)

- 청구항 방향: 노드 접근 패턴 분석 기반 연속 ID 할당 및 hyperbatch 스케줄링
- 응용: 대규모 소셜 네트워크, 지식 그래프 학습

### 2. Cross-stage KV 캐시 라우팅 (RelayGR)

- 청구항 방향: 파이프라인 단계 간 affinity 기반 요청 라우팅 및 계층적 캐시 관리
- 응용: 대규모 추천 시스템, 검색 엔진

### 3. MoE 장애 도메인 분리 (TARRAGON)

- 청구항 방향: Attention/Expert 분리 및 비동기 incremental KV 체크포인팅
- 응용: 대규모 MoE 모델 서빙 인프라

### 4. TDA 기반 KV 캐시 압축 (Warp-Cortex)

- 청구항 방향: 토폴로지 분석 기반 랜드마크 토큰 선택 및 가중치 공유
- 응용: Edge AI, 멀티에이전트 시스템

### 5. 매니폴드 인식 벡터 검색 (MCGI)

- 청구항 방향: LID 기반 적응형 빔 서치 및 I/O 예산 조절
- 응용: 대규모 벡터 데이터베이스, RAG 시스템

-----

## 후속 조치

- [ ] AGNES 논문 정독 - 스토리지 최적화 기법 상세 분석
- [ ] TARRAGON KV 체크포인팅 구현 방식 검토
- [ ] Warp-Cortex TDA 기반 압축 알고리즘 분석
- [ ] MCGI LID 계산 오버헤드 평가
- [ ] 특허 아이디어 5건 기술 검토 미팅 예약

-----

*Generated by storage-paper-analyzer skill | 2026-01-07*