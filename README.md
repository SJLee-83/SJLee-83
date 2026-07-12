# 이승재 (Seung-Jae Lee)

산업공학에서 데이터 분석과 시스템 최적화를 공부하다 소프트웨어 개발로 영역을 넓혔습니다. 최근에는 임베디드 펌웨어와 무선 OTA를 다루고 있고, RAG·LLM을 실제 제품에 적용하는 데 관심이 있습니다. 구현 전에 기능이 실제로 작동하는지 검증한 뒤 구현합니다.

<br>

## 기술 스택

**C · Renesas FSP(RA6E1) · ESP32**로 임베디드 펌웨어와 통신(MQTT · SPI · I2C)을 다뤘고, **Python · FastAPI**로 백엔드를, **React · Next.js · React Native**로 프론트엔드를 개발합니다. **RAG·LLM 응용**(LangGraph, Google Gemini, Azure OpenAI, pgvector)과 **CNN 비전**(TensorFlow, OpenCV)을 다뤘으며, 데이터는 **PostgreSQL · Redis**를 사용합니다. 산업공학 전공으로 **Simio · @Risk** 기반 이산사건 시뮬레이션과 통계 분석 경험이 있습니다.

<br>

## 대표 프로젝트 (Featured Projects)

### 1. RC Car OTA — SDV 개념 검증 (무선 펌웨어 업데이트 + 원격 제어)

> 차량을 무선으로 업데이트하고 원격 제어하는 SDV 개념을 RC카로 구현했습니다. 관제 GUI에서 MQTT로 명령을 보내면 ESP32 게이트웨이가 SPI로 차량 제어기(RA6E1)에 전달하고, 같은 경로로 펌웨어를 무선 배포합니다.

- **역할 (2인 팀):** RA6E1 주행 펌웨어(SPI Slave, I2C 모터·서보), 초음파 센서 기반 충돌 회피, **관제 GUI → MQTT → ESP32 → SPI 제어 체인 전체**. 별도로 **MCUboot 기반 서명 검증 OTA를 독립 시도**했습니다.
  - 팀원 담당: AI 음성 제어, Dual-Bank OTA(최종 시연 채택)
- **특징:**
  - SPI에는 흐름 제어가 없어, 슬레이브가 처리 중일 때 마스터가 밀어넣지 못하도록 **BUSY GPIO 핸드셰이크**를 설계했습니다.
  - 타이머 채널 확보가 어려워 **DWT 사이클 카운터**로 초음파 왕복시간을 μs 단위로 계측하고, 비차단 상태머신으로 통신과 센싱을 동시에 수행했습니다.
  - MQTT QoS1의 중복 수신을 전제로 멱등 처리하고, SUBACK 확인 후에만 OTA를 시작하도록 했습니다.
- **한계 (정직하게):** MCUboot는 코드 작성이 아니라 **FSP 설정 · 슬롯/서명 파이프라인 구성 · 실기 검증**입니다. **서명 검증 → 슬롯 교체 → 부팅**까지는 실기로 확인했지만, **코드 플래시 self-programming 제약**(실행 중인 코드 플래시에 쓰면 다음 명령을 fetch할 수 없어 hang)에 막혀 **무선 전송은 미완**입니다. 팀이 채택한 Dual-Bank 방식은 이 제약을 하드웨어 뱅크 전환으로 우회합니다.
- **기술:** C, Renesas FSP(RA6E1 / Cortex-M33), ESP32(Arduino), MQTT, SPI, I2C, PySide6, MCUboot(ECDSA P-256)

| 구분 | 링크 |
| :--- | :--- |
| **💻 GitHub (팀 전체)** | https://github.com/SJLee-83/rccar_ota_project |
| **💻 GitHub (MCUboot 부트로더)** | https://github.com/SJLee-83/rccar-mcuboot-ota |

<br>

### 2. R-Mate — FANUC 설비 에러 진단 AI (RAG + LangGraph)

> 유지보수 매뉴얼 기반 RAG와 LangGraph 조건부 라우팅으로, 설비 알람 코드에 대한 **안전 주의사항을 먼저 안내하는 한국어 조치 가이드를 출처와 함께** 생성합니다.

- **역할:** DACON 스마트 제조 AI 해커톤 본선 진출작(팀)의 **기획·설계를 리드**했고, 그 개념을 계승해 **코드는 처음부터 단독으로 재구현**했습니다.
- **특징:**
  - **검증 우선** — 기획 가정을 PoC로 먼저 검증하고 통과한 것만 구현합니다. 해커톤에서 핵심 차별점으로 내세웠던 "Cross-Reference RAG"는 매뉴얼 261페이지를 실측한 결과 `See Section` 참조가 **3회뿐**임을 확인하고 **기각**한 뒤, 마커 기반 청킹 + 메타데이터 필터링으로 재정의했습니다([ADR-002](https://github.com/SJLee-83/smart-mfg-ai-agent/blob/main/docs/decisions/ADR-002-cross-reference-rag-redefinition.md)).
  - 라우팅은 **규칙 기반**이고 LLM은 답변 노드 한 곳에서만 호출합니다. 검색 결과가 0건이면 LLM을 호출하지 않습니다. 필터 검색이 0건이면 필터를 풀고 **정확히 1회만** 재검색합니다.
  - 단위·통합 테스트 **63개**. 의존성 주입으로 각 그래프 노드를 독립적으로 검증합니다.
- **정직 고지:** 단일 턴·규칙 기반 라우팅이며 **자율 에이전트나 멀티에이전트가 아닙니다.** 답변 품질·검색 정확도는 아직 측정하지 않았습니다. 측정한 수치만 기재합니다(라우팅 25/25, 변형 입력 9/9).
- **기술:** Python, LangGraph, Google Gemini, ChromaDB(로컬 벡터 DB)

| 구분 | 링크 |
| :--- | :--- |
| **💻 GitHub** | https://github.com/SJLee-83/smart-mfg-ai-agent |

<br>

### 3. AI-SkinView — AI 챗봇 모듈 (팀 프로젝트)

> 피부 데이터(설문 + 안면 분석)를 바탕으로 맞춤 스킨케어 제품을 추천하는 대화형 챗봇. 팀 프로젝트 중 **AI 챗봇 모듈을 단독으로 담당**했습니다.

- **역할:** 챗봇 대화 상태 머신, RAG 기반 제품 추천(pgvector 코사인 검색), 프리셋 저장. 모놀리식 `ChatDAO`를 컨트롤러/서비스/리포지토리/캐시 **레이어드 아키텍처로 리팩터링**했습니다.
- **특징:**
  - 초기에는 LLM이 대화 분기를 판단하게 했으나, 대화가 길어질수록 환각이 심해져 사용자 의도를 잘못 파악했습니다. **상태별 버튼으로 분기를 명시적으로 고정**한 뒤 상태 전이가 어긋나는 문제가 사라졌습니다. 이 경험이 다음 프로젝트에서 "라우팅은 규칙으로, LLM은 생성에만"이라는 원칙으로 이어졌습니다.
  - 의도 분류 LLM의 출력을 그대로 신뢰하지 않고 **화이트리스트로 검증**해 범위를 벗어나면 폴백합니다.
- **한계 (정직하게):** 테스트가 없습니다. 추천 품질에 대한 정량 지표도 없습니다.
- **기술:** FastAPI, PostgreSQL + pgvector, Redis, Azure OpenAI, React Native

| 구분 | 링크 |
| :--- | :--- |
| **✨ 개인 포트폴리오 (리팩터링 버전)** | https://github.com/SJLee-83/ai-skin-chatbot |
| **👥 팀 프로젝트 원본** | https://github.com/SJLee-83/skinview-team-project |
| **📝 프로젝트 회고 (Notion)** | https://www.notion.so/AI-SkinView-239788271dd48086bfb6c5c3c777a138 |

<br>

### 4. Ant Insight — 투자 대시보드 (2인 팀)

> 환율·공포탐욕지수·주요 지수·종목 지표를 한 화면에 모아 보여주는 투자 대시보드. 2인 팀에서 **종목 상세 기능(백엔드 서비스 ~ 프론트엔드)**을 담당했습니다.

- **역할:** 기술지표·실루엣 구간·AI 인사이트의 백엔드 서비스와 종목 상세 화면을 수직으로 구현했습니다.
  - 팀원 담당: 홈 대시보드(환율·공포탐욕지수·지수), 투자 캘린더, 배포
- **특징:**
  - 지표 라이브러리(ta-lib) 없이 **pandas/numpy로 이동평균 · 볼린저밴드 · RSI(Wilder 지수평활) · 베타 계산식을 직접 작성**했습니다.
  - **실루엣 5구간** — 52주 고저 대비 현재가 위치를 5단계로 분류하고, 저점 구간인데 RSI가 과매수면 경고를 덧붙입니다. 단일 지표만 보고 판단하지 않도록 한 장치입니다.
  - **외부 API 실패 경로 설계** — Gemini 호출·파싱이 실패해도 Mock으로 폴백하고, TTL이 지난 캐시라도 있으면 반환합니다. AI 생성물이라는 면책을 항상 붙입니다. 외부 API가 실패해도 503을 던지지 않습니다.
- **기여 범위 (정직하게):** 제 기여는 원본 팀 저장소의 **`lee/feature-stock-detail` 브랜치(커밋 `9310f4e`)** 기준입니다. 이후 통합 과정에서 팀원이 프론트엔드를 리디자인하고 일부 지표 계산식을 개선했으므로, **현재 `main`의 코드가 모두 제 것은 아닙니다.**
- **기술:** Python, FastAPI, pandas/numpy, pykrx/yfinance, Google Gemini, Next.js, TypeScript

| 구분 | 링크 |
| :--- | :--- |
| **💻 GitHub (원본 팀 저장소 — 기여 근거)** | https://github.com/domcastle/antproject |
| **💻 GitHub (개인 정리본)** | https://github.com/SJLee-83/antproject |

<br>

### 5. CNN을 활용한 시력 보호 모델 (졸업 프로젝트)

> 웹캠으로 모니터와의 거리를 분류해, 너무 가까우면 경고로 눈의 피로를 줄이도록 돕는 CNN 모델입니다.

- **역할:** 원본은 **팀 졸업 프로젝트**이며, 제가 맡은 부분은 **학습 이미지 데이터 수집**, **경고 화면 프론트엔드**, **거리 분류 CNN 튜닝**입니다. 이 저장소는 개인 재구현본으로, **FastAPI 백엔드와 전처리·학습·예측 모듈 분리는 재구현하며 직접 추가**했습니다.
- **특징:** EfficientNetB0 전이학습(백본 동결), RetinaFace로 얼굴이 검출된 이미지만 남기는 학습 데이터 정제, ToF 센서로 실측한 거리를 파일명에 인코딩한 자동 라벨링.
- **성과:** 자체 테스트 분할 기준 분류 정확도 약 **82%** (단일 측정값이며 일반 사용 환경의 정확도를 보장하지는 않습니다).
- **기술:** Python, TensorFlow/Keras, OpenCV, FastAPI

| 구분 | 링크 |
| :--- | :--- |
| **💻 GitHub** | https://github.com/SJLee-83/C-Care |
| **📄 프로젝트 보고서 (PDF)** | https://github.com/SJLee-83/C-Care/blob/main/assets/C-Care-report.pdf |

<br>

## 학술 프로젝트 (Industrial Engineering)

> 산업공학 전공으로 데이터 분석·통계·최적화를 적용한 학부 프로젝트입니다(분석·기획 중심).

| 프로젝트 주제 | 자료 |
| :--- | :--- |
| **행정복지센터 민원 시스템 최적화 제안** — 현장 실측(220건) → @Risk 분포 적합 → Simio 이산사건 시뮬레이션 → Paired-t 검증. 창구 재배치로 대기시간 **−42.4%**(p<0.001) | [📄 보고서](https://github.com/SJLee-83/SJLee-83/raw/main/assets/죽전1동%20행정복지센터%20민원실%20대기시간%20감소%20및%20최적의%20민원%20시스템%20제안_보고서.pdf) |
| **담뱃값 인상에 따른 흡연율 및 판매량 예측 분석** — 회귀불연속(RDD) 기반 인과 분석 | [📄 보고서](https://github.com/SJLee-83/SJLee-83/raw/main/assets/담뱃값%20인상에%20따른%20흡연율%20차이%20분석%20및%20흡연율,%20담배%20판매량%20예측_보고서.pdf) ｜ [📊 발표자료](https://github.com/SJLee-83/SJLee-83/raw/main/assets/담뱃값%20인상에%20따른%20흡연율%20차이%20분석%20및%20흡연율,%20담배%20판매량%20예측_ppt.pdf) |
| **살균기 데이터를 활용한 식품 살균 공정에서의 품질 예측** — 의사결정나무 튜닝으로 불량 탐지율 55% → 98%. 트리의 분기 규칙을 현장 온도 조건으로 변환 | [📊 발표자료](https://github.com/SJLee-83/SJLee-83/blob/main/assets/%EC%82%B4%EA%B7%A0%EA%B8%B0%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%A5%BC%20%ED%99%9C%EC%9A%A9%ED%95%9C%20%EC%8B%9D%ED%92%88%20%EC%82%B4%EA%B7%A0%20%EA%B3%B5%EC%A0%95%EC%97%90%EC%84%9C%EC%9D%98%20%ED%92%88%EC%A7%88%20%EC%98%88%EC%B8%A1.pdf) |
| **MFC 구축을 위한 입지 요건 분석** — 로지스틱 회귀 | [📊 발표자료](https://github.com/SJLee-83/SJLee-83/blob/main/assets/GS%20%EC%B9%BC%ED%85%8D%EC%8A%A4%20MFC%EB%A5%BC%20%ED%99%9C%EC%9A%A9%ED%95%9C%2C%20MFC(Micro%20Fulfillment%20Center)%20%EA%B5%AC%EC%B6%95%EC%9D%84%20%EC%9C%84%ED%95%9C%20%EC%9E%85%EC%A7%80%20%EC%9A%94%EA%B1%B4%20%EB%B6%84%EC%84%9D.pdf) |
| **컨조인트 분석을 이용한 치킨 주문 행태 연구** | [📄 보고서](https://github.com/SJLee-83/SJLee-83/raw/main/assets/컨조인트%20분석을%20이용한%20치킨%20주문%20이용실태%20및%20선택속성에%20대한%20연구_보고서.pdf) ｜ [📊 발표자료](https://github.com/SJLee-83/SJLee-83/raw/main/assets/컨조인트%20분석을%20이용한%20치킨%20주문%20이용실태%20및%20선택속성에%20대한%20연구_ppt.pdf) |

<br>

## 연락처 (Contact)

- **Email:** greatsjlee@gmail.com
