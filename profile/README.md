# BOAZ Signal

**금융상품 설명서의 이해하기 어려운 지점을 데이터로 분석합니다.**

BOAZ Signal은 판매 중인 금융상품 설명서를 수집·분석해 **설명 난독성 지도**를 만드는 프로젝트 팀입니다. 설명서의 어느 부분이 읽기 어려운지, 같은 상품군과 위험등급 안에서 설명 난도가 어떻게 다른지 확인하고, 분석 결과를 비대면 가입 절차의 개선안으로 연결합니다.

프로젝트 주제는 **금융상품 설명 리스크 진단 파이프라인**입니다. 고객이 가입 전에 접하는 공개 설명서를 분석 대상으로 삼아, 문장의 복잡함·전문용어·정보 배치가 이해에 주는 부담을 살펴봅니다.

[프로젝트 설계](https://github.com/BOAZ-Signal-Team-26/signal-pipeline/tree/main/docs) · [데이터 검증 기록](https://github.com/BOAZ-Signal-Team-26/signal-pipeline/tree/main/research) · [프로젝트 계획](https://github.com/BOAZ-Signal-Team-26/Project-Management)

## 우리가 만드는 것

| 산출물 | 목표 |
|---|---|
| 전수 채점 파이프라인 | 대상 설명서를 수집하고, 텍스트를 추출해 부·절별 설명 난독성을 채점 |
| 설명 난독성 대시보드 | 상품별 점수 분포를 비교하고, 개별 설명서의 부·절별 점수와 감점 사유를 확인 |
| 비대면 가입 절차 개선안 | 펀드·ELS의 앱 가입 절차를 실측하고, 설명 방식과 정보 제시 순서의 개선 항목을 도출 |
| 파이프라인 문서 | 아키텍처 다이어그램, 데이터 스키마, 데이터 품질 리포트를 정리 |

원본 문서부터 채점 근거까지 추적할 수 있도록 데이터 구조를 설계하며, 수집·파싱 실패와 계산 불가 사유도 함께 기록합니다.

> 현재는 데이터 설계와 원천 데이터 검증을 진행하고 있습니다. 위 산출물은 개발 목표이며, 설계 단계와 승인 여부는 [설계 문서 안내](https://github.com/BOAZ-Signal-Team-26/signal-pipeline/blob/main/docs/README.md)에서 확인할 수 있습니다.

## 무엇을 분석하나요?

현재 범위는 공모펀드·ETF·ELS이며, 설명서 채점과 가입 절차 분석의 대상은 다음과 같습니다.

| 상품군 | 설명 난독성 채점 | 가입 절차 개선안 |
|---|:---:|:---:|
| 공모펀드 | 대상 | 대상 |
| ETF | 대상 | 제외 |
| ELS | 제외 | 대상 |

**CDI는 설명 난독성을 나타내는 지표**입니다. 높은 값은 더 어려운 설명을 뜻하며, 동일 상품군·동일 위험등급의 비교 집단 안에서 해석합니다. 설명서의 부·절을 채점하고, 펀드 단위로 집계해 비교하는 구조를 설계하고 있습니다.

상세 산식과 배점은 검토 중입니다. 평가 단위와 비교 기준은 [점수 저장과 비교 모집단](https://github.com/BOAZ-Signal-Team-26/signal-pipeline/blob/main/docs/scoring-and-population.md), 상품군별 범위는 [작업 범위와 WBS](https://github.com/BOAZ-Signal-Team-26/Project-Management/blob/main/docs/06-wbs.md)를 따릅니다.

### 평가 관점과 검증 방향

| 검토 중인 평가 관점 | 확인하려는 것 |
|---|---|
| 언어 복잡도 | 문장 길이 등 읽기에 부담을 주는 언어적 특성 |
| 용어 부담 | 전문용어의 비중과 용어 설명 여부 |
| 구조 접근성 | 필요한 정보를 찾기 쉬운지, 문서 구성과 참조 방식이 적절한지 |
| 고지 충실도 | 상품에 적용되는 설명 항목이 문서에 포함되어 있는지 |

고지 충실도를 CDI에 합칠지 별도 점수로 보고할지, 어떤 상품군에 적용할지는 논의 중입니다. 사람의 이해도 평가와 LLM 평가를 병행하는 검증을 준비하며, 세부 문항·표본·판정 기준은 파일럿과 팀 논의를 거쳐 정할 예정입니다. 분쟁·제재 자료를 검증과 사례 탐색에 활용하는 범위도 검토하고 있습니다.

## 데이터를 결과로 연결하는 과정

아래는 개발 목표를 요약한 흐름입니다.

```mermaid
flowchart LR
    A[공시 문서·상품 정보 수집] --> B[텍스트 추출·부절 분해]
    B --> C[LLM 고지 항목 추출]
    B --> D[설명 난독성 채점]
    C --> E[분석 결과 적재]
    D --> E
    E --> F[대시보드·문서별 상세 조회]
```

수집·파싱·추출 과정의 누락률, 실패율, 일관성을 점검하고 원본·가공 데이터·분석 결과를 구분해 관리합니다. 가입 절차 개선안은 앱 화면을 직접 조사하고 분석 결과와 함께 검토해 작성합니다.

주요 데이터 소스와 역할은 다음과 같습니다. 세부 수집 범위와 구현 계약은 설계 문서에서 관리합니다.

| 소스 | 활용 |
|---|---|
| DART | 투자설명서 원문과 공시 정보 확보 |
| 금융투자협회 전자공시 | 펀드 공시·첨부 문서, 판매회사와 펀드의 관계 확인 |
| 공공데이터포털 펀드상품기본정보 | 펀드 코드와 기본정보를 활용한 소스 간 연결 |
| KRX | ETF 식별과 일별 목록 확인 |
| 금융감독원 | 제재·경영유의사항과 분쟁조정 사례의 검증·탐색 활용 검토 |

수집 방식과 실측 근거는 [데이터 소스 수집 명세](https://github.com/BOAZ-Signal-Team-26/signal-pipeline/blob/main/docs/data-sources.md)에 정리합니다.

## 저장소 안내

| 저장소 | 역할 | 처음 볼 문서 |
|---|---|---|
| [signal-pipeline](https://github.com/BOAZ-Signal-Team-26/signal-pipeline) | 수집·파싱·채점·추출 코드, 데이터 설계, 원천 데이터 검증 | [설계 문서 안내](https://github.com/BOAZ-Signal-Team-26/signal-pipeline/blob/main/docs/README.md) |
| [signal-infra](https://github.com/BOAZ-Signal-Team-26/signal-infra) | 클라우드 리소스, 네트워크와 환경 설정, 배포 구성 | [인프라 범위](https://github.com/BOAZ-Signal-Team-26/signal-infra/blob/main/README.md) |
| [Project-Management](https://github.com/BOAZ-Signal-Team-26/Project-Management) | 일정, 작업 분해, 스프린트 계획과 협업 규칙 | [프로젝트 안내](https://github.com/BOAZ-Signal-Team-26/Project-Management/blob/main/README.md) |

프로젝트를 처음 살펴본다면 **설계 문서 안내 → 데이터 모델 → 데이터 소스 수집 명세** 순서로 읽어보세요. 개발 환경 설정은 [signal-pipeline README](https://github.com/BOAZ-Signal-Team-26/signal-pipeline/blob/main/README.md)에 있습니다.

## 팀과 협업

| 역할 | 팀원 |
|---|---|
| 팀 리드·PM | 대현 |
| 분석·리서치 | 민석 |
| 데이터 사이언스 | 다빈 |
| 데이터 엔지니어링·인프라 | 주영 |

2주 단위 스프린트와 매주 수요일 정례 미팅으로 진행합니다. 코드·설계·계획은 GitHub에서 관리하고, 회의록·의사결정·티켓 진행 상황은 팀 Notion에서 관리합니다. 자료별 기준 문서의 위치는 [Project-Management 안내](https://github.com/BOAZ-Signal-Team-26/Project-Management/blob/main/README.md)에 정리되어 있습니다.

<!-- 작성 근거: 저장소 현행 문서와 Signal Team Notion을 2026년 9월 30일 대조.
9월 30일 문서는 안건 사전 정리 상태로, 범위 변경·점수 합산·ERD 개편·일정 조정 제안을 확정 사실로 반영하지 않음.
-->
