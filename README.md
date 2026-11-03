# 김은진 | AI Engineer

데이터 전처리와 MLP·TabNet 모델 실험, RAG 데이터 구축·독립 QA, React 화면과 API 연동을 진행했습니다. 프로젝트별 담당 작업과 문제 해결 과정, 검증 결과를 정리했습니다.

## 대표 프로젝트

| 프로젝트 | 해결하려는 문제 | 내가 맡은 일 |
|---|---|---|
| [**TrendIt**](https://github.com/eunjin661/SKN29-2nd-1Team) | 초기 관측 정보로 유튜브 트렌딩 특성을 분석·예측 | 공동 전처리, MLP·TabNet 실험, 후속 평가·재현성 개선 |
| [**WaterBridge**](https://github.com/eunjin661/SKN29-FINAL-4TEAM) | 정수기 매뉴얼을 근거로 문의에 대응하고 상담으로 연결 | RAG 데이터 인계, 청킹 비교 독립 재검증, 데이터 QA |
| [**A-FIT**](https://github.com/eunjin661/SKN29-4TH-3Team) | 사용자 조건과 모집공고를 연결해 청약 준비를 지원 | 서비스 기획, React 화면 구현, UI·API 연동 |

---

## TrendIt · 모델 실험과 평가 재현성 개선

**개인 담당:** 공동 데이터 전처리, MLP·TabNet 실험  
**주요 기술:** Python · pandas · PyTorch · TabNet · scikit-learn

### 담당 작업과 문제 해결

- **모델 실험:** 범주형 임베딩과 수치 특성을 결합하고 Residual 연결을 포함한 MLP를 실험했습니다. TabNet과 비교한 원본 코드·노트북을 해시와 함께 보존했습니다.
- **평가 누수 보완:** 개인 저장소 후속 개선에서 예측 시점에 알 수 없는 최종 지속 시간·파생 정답의 입력 문제를 다뤘습니다. 시간순 분할과 영상 ID 중복 제거, Train에서만 계산하는 정답 기준으로 평가 경로를 보완했습니다.
- **비교 조건 통일:** 같은 v2 조건에서 기준선과 딥러닝을 포함한 8개 모델을 비교했습니다. Validation으로 모델과 임계값을 선택하고 Test 결과와 구분했습니다.
- **서비스 결과 정합성:** 고정 성능·샘플 결과를 반환하던 구버전 API 13개를 명시적 폐기 응답으로 전환하고, 기본 API 문서의 경로를 38개에서 현재 계약 25개로 정리했습니다.

### 결과와 검증

- **Test ROC-AUC 0.8128 · F1 0.6653** — 시간순·영상 중복 제거 v2 평가, Test 4,986건 / 고유 영상 2,311개, 2026-10-22 기록.
- 같은 평가의 Logistic 기준선 AUC는 0.6247이며, 선택한 Embedding MLP와의 차이는 **+0.1881**입니다.
- 후속 API 정리의 로컬 회귀는 **Backend 122건·Browser 49건 통과**로 기록했습니다(2026-11-02).

팀 프로젝트 당시의 개인 실험과 이후의 공통 평가·API 보완은 구분했습니다. 후속 보완에는 코딩 도구를 활용했습니다.

[개인 실험 원본](https://github.com/eunjin661/SKN29-2nd-1Team/blob/main/docs/experiments/ORIGINAL_EXPERIMENT_PROVENANCE.json) · [평가 조건·모델 비교](https://github.com/eunjin661/SKN29-2nd-1Team/blob/main/docs/experiments/T0_V2_RESULTS.md) · [API 정리·회귀 기록](https://github.com/eunjin661/SKN29-2nd-1Team/blob/main/docs/experiments/CLOSURE_RESULTS_20261102.md)

---

## WaterBridge · RAG 데이터와 독립 QA

**개인 담당:** Data · QA · DevOps  
**주요 작업:** 문서·근거 계보, 청킹 재검증, 평가셋 검수 자료, 데이터 재생성 검증

### 담당 작업과 문제 해결

- **청킹 비교 독립 재검증:** 팀에서 구현한 Runner를 별도 환경에서 재실행했습니다. 청킹 설정 5종 × 제품 필터 2종 × 질문 35건의 **비교 결과 350건**과 입력 해시·실행 조건을 기록했습니다.
- **검색 실패 분석:** 무출수·누수 질문의 검색 실패를 살펴보고, 페이지 단위 Evidence 병합과 원문 구간 누락의 영향을 분석했습니다.
- **데이터 인계본 생성:** 원문의 행 단위 Child 15건과 누락 구간 보존 Chunk 5건을 포함해 **검색 후보 111건의 Corpus v2 인계본**을 생성했습니다. 문서·페이지·행과 검색 근거의 연결을 함께 보존했습니다.
- **평가셋 검수 준비:** Gold 질문 60건의 근거·위험도·안내 정책을 대조하는 검수 자료를 정리했습니다. 자동 대조 결과와 사람의 최종 승인 상태를 구분했습니다.
- **재현성·인계 QA:** Source Hash, Schema, 식별자 Mapping, Manifest와 재생성 결과를 검토하고 변경 여부를 확인했습니다.

### 결과와 검증

- 2026-08-24 기준 **데이터 단위 테스트 135건 통과**, Pipeline QA **60파일·990레코드** 검증 기록.
- 같은 기록에서 재생성 후 **변경 파일 0건·canonical drift 0건** 확인.
- Corpus는 데이터 인계본이며, Gold 최종 승인과 운영 Profile 반영은 후속 검증 범위로 구분했습니다.

[청킹 비교·실패 분석](https://github.com/eunjin661/SKN29-FINAL-4TEAM/blob/main/docs/testing/rag/b1-chunking-comparison-v1_%EA%B9%80%EC%9D%80%EC%A7%84_%EC%9E%AC%EA%B2%80%EC%A6%9D_20260812.md) · [Corpus·Gold 인계와 QA](https://github.com/eunjin661/SKN29-FINAL-4TEAM/blob/main/docs/testing/rag/full-corpus-v2-gold-primary-review_20260823.md) · [Data Owner Review](https://github.com/eunjin661/SKN29-FINAL-4TEAM/blob/main/docs/individual/eunjin/20260730_%EB%8D%B0%EC%9D%B4%ED%84%B0_Owner_Review.md)

---

## A-FIT · 서비스 기획과 React UI·API 연동

**개인 담당:** Frontend · Planning  
**주요 기술:** React · TypeScript · Vite · REST API

청약 초보자가 **프로필 입력 → 진단 요청 → 결과 확인 → 이력 조회**로 이어지는 과정을 이용할 수 있도록 화면과 사용자 흐름을 구성했습니다.

### 담당 작업과 구현 내용

- **사용자 흐름 기획:** 프로필·진단·결과 화면을 연결하고, 입력한 조건과 분석 결과를 다시 확인하는 흐름을 정리했습니다.
- **React 화면·API 연동:** React/Vite 기반 UI를 구현하고 청약 조건 입력, 진단 요청, 결과 표시를 API와 연결하는 작업에 참여했습니다.
- **상태를 고려한 UI 개선:** 조건부 입력, 요청 중 로딩·중복 실행 방지, API 오류 안내를 포함한 화면 개선에 참여했습니다.

### 확인할 결과물

- 프로필 입력·진단·결과 화면의 React 코드와 화면별 API 연결.
- 입력·로딩·성공·오류·빈 상태를 정리한 팀 화면설계서와 구현 산출물.

[팀 역할](https://github.com/eunjin661/SKN29-4TH-3Team#1-팀-구성) · [프로필 화면 코드](https://github.com/eunjin661/SKN29-4TH-3Team/blob/main/frontend-react/src/app/pages/Profile.tsx) · [결과 화면 코드](https://github.com/eunjin661/SKN29-4TH-3Team/blob/main/frontend-react/src/app/pages/ResultDetail.tsx) · [화면설계서](https://github.com/eunjin661/SKN29-4TH-3Team/blob/main/docs/final/%ED%99%94%EB%A9%B4%EC%84%A4%EA%B3%84%EC%84%9C.md)

<details>
<summary>대표 UI 보기 · 청약 프로필 입력</summary>

<img src="https://raw.githubusercontent.com/eunjin661/SKN29-4TH-3Team/main/docs/assets/readme/screens/03-profile.png" width="760" alt="A-FIT 청약 프로필 입력 화면" />

프로젝트 README에 보관된 팀 서비스 화면입니다.

</details>

---

## 사용한 기술과 작업 영역

| 영역 | 기술·경험 |
|---|---|
| 데이터·모델 실험 | Python, pandas, scikit-learn, PyTorch, TabNet |
| RAG 데이터·QA | 청킹 비교, Evidence 계보, 평가셋 검수 자료, 재생성 검증 |
| 프론트엔드·연동 | React, TypeScript, Vite, REST API |
| 실행·회귀 | Git, Docker, GitHub Actions, 테스트·실행 근거 기록 |

