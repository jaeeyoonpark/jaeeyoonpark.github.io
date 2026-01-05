---
share: true
---
# Roundup Copilot

**AI/Storage 논문 자동 수집 및 분석 파이프라인**

---

## 1. 개요

Roundup Copilot은 매일 발표되는 AI/ML 논문 중 **스토리지 시스템과 관련된 논문을 자동으로 발굴하고 분석**하는 반자동 파이프라인이다.

### 1.1 한눈에 보는 전체 흐름

![[roundup_copilot_overview.png|roundup_copilot_overview.png]]

### 1.2 설계 철학

다량의 논문을 자동으로 모으고, 동일한 깊이로 분석하는 데 효율적인 방식이 필요하다. Roundup Copilot은 **2단계 Funnel 전략**을 사용한다:

||Stage 1|Stage 2|
|:--|:--|:--|
|**목표**|Recall (놓치지 않기)|Precision (정확한 평가)|
|**실행**|완전 자동 (GitHub Actions, 매일)|반자동 (사용자 트리거 + Claude)|
|**분석**|제목 + Abstract|Abstract + PDF 전문|
|**출력**|이메일 ~300편 (10% 중요 / 90% 참고)|필독 목록 + 개요 정리|

---

## 2. 배경

### 2.1 문제 정의

arXiv, OpenReview 등에서 매일 수백 편의 AI/ML 논문이 쏟아진다. 관심 영역의 연구 동향을 놓치지 않고 추적하기 어렵다.

### 2.2 기존 방식의 한계

수동 검색은 시간이 많이 든다. 단순 키워드 매칭으로는 "직접 스토리지를 다루진 않지만 시사점이 있는 논문"을 놓치기 쉽다.

### 2.3 목표

AI 알고리즘 동향과 관련 스토리지 연구 흐름을 지속적으로 파악한다.

---

## 3. Stage 1: 자동 수집 + 필터링

GitHub Actions 기반 자동화 파이프라인이다. 매일 논문을 수집하고, 키워드 필터와 LLM 스코어링을 거쳐 이메일로 발송한다.

### 3.1 워크플로우

![[roundup_copilot_stage1.png|roundup_copilot_stage1.png]]

|단계|입력|처리|출력|
|:--|:--|:--|:--|
|논문 수집|arXiv, OpenReview, Semantic Scholar|API 호출 + 중복 제거|논문 메타데이터|
|키워드 필터|전체 수집 논문|Abstract 키워드 매칭|~300편 통과|
|LLM 스코어링|키워드 통과 논문|GPT-4o-mini 평가|1-8점 + 사유|
|이메일 발송|스코어링 결과|Gmail SMTP|[논문 Daily] 이메일|

### 3.2 실행 스케줄

|항목|값|
|:--|:--|
|트리거|GitHub Actions (cron)|
|실행 시간|매일 KST 06:00 (UTC 21:00)|
|수동 실행|`workflow_dispatch`로 가능|

### 3.3 데이터 소스

|소스|대상|설정|
|:--|:--|:--|
|**arXiv**|cs.AI, cs.LG, cs.DC, cs.AR|카테고리당 최대 200편|
|**OpenReview**|NeurIPS, ICLR, ICML, MLSys|최신 submission|
|**Semantic Scholar**|AI/ML 관련 검색|최대 100편|

### 3.4 arXiv 인덱싱 지연 (Critical)

arXiv는 논문이 **제출된 날짜(published_date)**와 **API에서 검색 가능해지는 시점** 사이에 **1-3일 지연**이 발생한다. 이는 arXiv의 내부 검토 및 인덱싱 프로세스 때문이다.

```
논문 제출 (Day 0)
     ↓
arXiv 검토 (Day 0-1)
     ↓
인덱싱 완료 (Day 1-3)  ← API에서 검색 가능
     ↓
GitHub Actions 수집
```

#### 주요 특성

|특성|설명|
|---|---|
|**평상시 지연**|1-2일 (논문 제출 → API 반영)|
|**주말/휴일 지연**|2-3일+ (인덱싱 프로세스 중단)|
|**인덱싱 재개 시**|밀린 논문들이 한꺼번에 등장|

#### 실제 사례 (2025-12-30 ~ 2026-01-02)

```
Timeline:
- Dec 30 (월): 논문 제출됨
- Dec 31 (화): arXiv 연말 휴무
- Jan 1 (수): 새해 휴일 - 인덱싱 중단
- Jan 2 (목): 인덱싱 재개, Dec 30 논문이 API에 등장

결과:
- Jan 1 이메일: Dec 30 논문 없음 (아직 인덱싱 안됨)
- Jan 2 이메일: Dec 30 논문 다수 포함 (인덱싱 완료됨)
```

#### days_back 설정

```python
def collect_arxiv(..., days_back: int = 3):
    """
    days_back=3 설정 이유:
    1. 평상시 인덱싱 지연 (1-2일) 커버
    2. 주말/휴일 지연 (2-3일) 커버
    3. 논문 누락 방지 (False Negative 최소화)
    """
    date_cutoff = target_date - timedelta(days=days_back)
```

|days_back|장점|단점|
|---|---|---|
|1|API 호출 효율|휴일 후 논문 누락|
|**3 (권장)**|인덱싱 지연 커버|약간의 중복 처리|
|7|완벽한 커버리지|불필요한 중복 증가|

### 3.5 중복 제거 메커니즘

`days_back=3`으로 인해 동일 논문이 여러 번 수집될 수 있다. 이를 방지하기 위해 다단계 중복 제거를 수행한다.

#### 중복 제거 단계

|단계|방식|목적|
|---|---|---|
|URL 기반|동일 URL 제거|소스 내 중복|
|제목 기반|정규화된 제목 비교|교차 소스 중복 (arXiv ↔ Semantic Scholar)|
|seen_papers|이전 수집 기록 비교|날짜 간 중복|

#### seen_papers.json 동작

```
Day 1: 논문 A 수집 → seen_papers.json에 추가
Day 2: 논문 A 재수집 시도 → seen 확인 → 스킵
Day 3: 새 논문 B만 수집
```

#### seen_papers.json 구조

```json
{
    "paper_ids": [
        "http://arxiv.org/abs/2512.21835",
        "http://arxiv.org/abs/2512.22036",
        ...
    ],
    "last_updated": "2026-01-01T21:29:43.119983"
}
```

**제한**:

- 최대 5,000개 ID 유지 (무한 증가 방지)
- 오래된 ID는 자동 삭제 (약 30일치)
- 이메일 발송 성공 후에만 업데이트

### 3.6 키워드 필터

Abstract에 아래 키워드가 포함된 논문만 통과시킨다:

|카테고리|키워드|
|:--|:--|
|스토리지/메모리|storage, memory, checkpoint, cache, offload, ssd, disk, i/o, bandwidth, throughput|
|컴퓨팅/분산|compute, distribut, parallel, large-scale, scaling|
|추론 최적화|inference, kv cache, batch, serving, latency|
|학습 최적화|training, data load, pipeline, prefetch|
|모델 구조|moe, mixture of experts, sparse, quantiz, compress|

### 3.7 LLM 스코어링

키워드 필터를 통과한 논문에 대해 LLM이 스토리지 관련성을 1-8점으로 평가한다.

|항목|값|
|:--|:--|
|모델|GPT-4o-mini|
|평가 기준|스토리지/메모리 시스템 관점 관련성 (부록 참조)|

5점 이상은 상세 분석, 3-4점은 제목만 기록, 1-2점은 제외한다.

### 3.8 이메일 발송

|항목|값|
|:--|:--|
|방식|Gmail SMTP|
|형식|메타데이터(JSON) 본문 + Markdown 첨부|
|제목 예시|`[논문 Daily] 2025-01-15|

이메일 본문에는 메타데이터(JSON)가 포함된다. 상세 분석 결과는 첨부된 Markdown 파일에서 확인할 수 있다.

### 3.9 휴일/주말 처리

#### 인덱싱 중단 기간

|기간|영향|
|---|---|
|미국 공휴일 (Christmas, Thanksgiving 등)|1-2일 지연|
|새해 (Dec 31 ~ Jan 1)|2-3일 지연|
|주말|경미한 지연 (보통 정상 동작)|

#### 대응 방안

1. **days_back=3 유지**: 휴일 지연 커버
2. **seen_papers 자동 관리**: 중복 없이 밀린 논문 수집
3. **수동 확인 불필요**: 시스템이 자동으로 처리

### 3.10 트러블슈팅

#### 증상: 특정 날짜 논문이 다음 날 이메일에 포함됨

**원인**: arXiv 인덱싱 지연 (정상 동작)

**확인 방법**:

```bash
# seen_papers.json의 last_updated 확인
cat data/seen_papers.json | jq '.last_updated'

# 특정 논문이 언제 수집되었는지 확인
cat data/seen_papers.json | jq '.paper_ids[]' | grep "2512.21835"
```

#### 증상: 동일 논문이 여러 번 이메일에 포함됨

**원인**: seen_papers.json 저장 실패

**해결**:

```yaml
# GitHub Actions workflow에서 commit 단계 확인
- name: Commit seen papers
  run: |
    git add data/seen_papers.json
    git diff --staged --quiet || git commit -m "Update seen papers"
    git push
```

---

## 4. Stage 2: Claude AI 심층 분석

storage-paper-analyzer skill 기반 정밀 분석이다. Stage 1에서 이메일로 받은 논문 중 관심 있는 것을 Claude와 함께 심층 분석한다.

### 4.1 워크플로우

![[roundup_copilot_stage2.png]]

|단계|입력|처리|출력|
|:--|:--|:--|:--|
|ID 추출|[논문 Daily] 이메일|본문에서 arXiv ID 파싱|checkpoint_1_ids.txt|
|Abstract Screening|arXiv ID 목록|Abstract 기반 예측 점수|checkpoint_3_screening.json|
|PDF Deep Analysis|예측 ≥4점 논문|PDF 전문 분석|deep_analysis_results.md|
|최종 보고서|분석 결과 통합|점수별 분류 및 정리|final_report.md|

### 4.2 분석 트리거

|항목|값|
|:--|:--|
|실행 방식|사용자 트리거 (반자동)|
|트리거 예시|"어제 이메일 논문 분석해줘", "논문 Daily 분석"|
|입력 소스|Gmail ([논문 Daily] 이메일), arXiv ID 직접 입력, PDF 업로드|

### 4.3 Abstract Screening

모든 논문에 대해 Abstract만으로 예측 점수를 부여하여 상세 분석 대상을 선별한다.

|항목|값|
|:--|:--|
|입력|Stage 1 이메일의 모든 arXiv ID|
|분석 기반|Abstract만 사용 (PDF 다운로드 없음)|
|평가 기준|8점 스코어링 기준 적용 (부록 참조)|
|임계값|예측 ≥4점 → PDF 분석 대상|

### 4.4 PDF Deep Analysis

예측 ≥4점 논문만 PDF 전문을 분석한다.

|항목|값|
|:--|:--|
|대상|Abstract Screening에서 예측 ≥4점인 논문|
|분석|PDF 전문 다운로드 + 텍스트 추출|
|평가 기준|8점 스코어링 기준 적용 (부록 참조)|
|출력|4-Point Summary, 최종 점수, Patent Ideas|

**4-Point Summary 구조:**

|항목|내용|
|:--|:--|
|해결하려는 문제|논문이 다루는 핵심 문제|
|기존 방식 한계|기존 접근법의 문제점|
|제안 방법|논문의 핵심 기여|
|효과|실험 결과 및 개선 효과|

### 4.5 최종 보고서 구조

분석 완료 후 `final_report.md` 형태로 결과를 정리한다.

|섹션|내용|
|:--|:--|
|고득점 논문 (≥5점)|4-Point Summary + 8점 스코어링 + Patent Ideas|
|검토 논문 (3-4점)|제목, 예측→최종 점수, 주요 내용 테이블|
|스킵 논문 (≤2점)|제목, arXiv ID, 스킵 사유 테이블|
|분석 통계|Stage 1/2 처리량, 점수 분포, 효율성|

**체크포인트 파일:**

|파일|내용|
|:--|:--|
|checkpoint_1_ids.txt|arXiv ID 목록|
|checkpoint_2_metadata.json|제목, Abstract, PDF URL|
|checkpoint_3_screening.json|예측 점수, 상세분석 대상 여부|
|deep_analysis_results.md|PDF 상세 분석 결과|
|final_report.md|최종 통합 보고서|

---

## 5. 사용 예시

(작성 예정)

---

## 6. 기술 스택

### 6.1 Infrastructure

|항목|기술|
|:--|:--|
|CI/CD|GitHub Actions|
|런타임|Python 3.11|
|실행 환경|Ubuntu (GitHub-hosted runner)|
|설정 관리|YAML (config/config.yaml)|
|시크릿|GitHub Secrets|

### 6.2 Stage 1 구성요소

|구성요소|기술|용도|
|:--|:--|:--|
|논문 수집|arxiv, openreview-py, requests|arXiv, OpenReview, Semantic Scholar API|
|LLM 스코어링|OpenAI API (gpt-4o-mini)|스토리지 관련성 1-8점 평가|
|이메일 발송|smtplib (Gmail SMTP)|일일 Digest 발송|
|유틸리티|pyyaml, python-dotenv|설정 및 환경변수 관리|

### 6.3 Stage 2 구성요소

|구성요소|기술|용도|
|:--|:--|:--|
|분석 엔진|Claude AI|Abstract Screening + PDF 분석|
|스킬|storage-paper-analyzer|2단계 분석 워크플로우|
|논문 수집|fetch_papers.py (arXiv API)|메타데이터 + PDF URL 수집|
|PDF 처리|pdftotext|PDF 텍스트 추출|
|이메일 연동|Gmail API|[논문 Daily] 이메일에서 ID 추출|

---

## 부록: 8점 스코어링 기준

Stage 1 (GPT-4o-mini)과 Stage 2 (Claude) 모두 동일한 스코어링 프레임워크를 사용한다.

### 평가 항목

| 기준                       | 점수  | 평가 내용                                                  |
| :----------------------- | :-- | :----------------------------------------------------- |
| Direct Storage Relevance | 0-3 | Storage, SSD, I/O, memory hierarchy 명시적 언급             |
| Algorithmic Implications | 0-3 | KV-cache, checkpoint, offloading, parameter loading 관련 |
| System Impact            | 0-2 | Compute→Memory/Storage bottleneck 전환 가능성               |

### 점수별 판정

|점수|판정|Stage 1 처리|Stage 2 처리|
|:--|:--|:--|:--|
|7-8|직접적 시사점 + 새로운 접근|상세 분석|필독 + Action Items|
|5-6|관련성 있음, 참고할 만함|상세 분석|필독|
|3-4|간접적 관련, 동향 파악용|제목만 기록|검토|
|1-2|관련 없음|제외|스킵|

### False Negative 방지 원칙

Storage 관련 가능성이 조금이라도 있으면 높은 점수를 부여한다. 애매한 경우 상세 분석 대상으로 분류하여 놓치는 것을 방지한다.

---

## 부록: 날짜 관련 핵심 개념 요약

|개념|설명|설정값|
|---|---|---|
|**published_date**|논문이 arXiv에 제출된 날짜|-|
|**인덱싱 완료일**|API에서 검색 가능해지는 날짜|published_date + 1~3일|
|**days_back**|수집 시 과거 며칠까지 볼지|3일 (권장)|
|**seen_papers**|중복 방지용 수집 기록|최대 5,000개|
|**GitHub Actions 실행**|KST 06:00 (UTC 21:00)|매일|

---

**Repository**: https://github.com/jaeeyoonpark/paper_daily_digest




