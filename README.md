# 이승재

산업공학에서 데이터 분석과 시스템 최적화를 공부하다 소프트웨어 개발로 영역을 넓혔고, 최근에는 임베디드 펌웨어·무선 OTA와 RAG·LLM 응용을 다룹니다.

<br>

## 기술 스택

**C · Renesas FSP(RA6E1) · ESP32**로 임베디드 펌웨어와 통신(MQTT · SPI · I2C)을, **Python · FastAPI**로 백엔드를, **React · Next.js · React Native**로 프론트엔드를 개발합니다. **RAG·LLM 응용**(LangGraph, Google Gemini, Azure OpenAI, pgvector)과 **CNN 비전**(TensorFlow, OpenCV)에 **PostgreSQL · Redis**를 함께 쓰며, 산업공학 전공으로 **Simio · @Risk** 이산사건 시뮬레이션과 통계 분석 경험이 있습니다.

<br>

## 대표 프로젝트

### 1. 다태워: RC카 무인 자율주행 택시

> 어르신을 위한 무인 이동 서비스를 소형 RC카(Jetson Orin Nano)로 구현한 SSAFY 공통프로젝트로, 실내 5×3m 트랙에서 호출·배차·주행·도착 보고까지 사람이 개입하지 않고 수행합니다. 시연 당일 본 시연 1회를 무개입 완주했고, 도착 오차 12.2cm와 8.7cm를 기록했습니다.

- **역할 (5인 팀):** 차량 임베디드 전체. 측위(ArUco 좌표 검증·보정·추측항법), 경로 계획(차선 그래프 + Dijkstra), 주행 제어(직선 P 제어 + 노면표시 기반 회전 트리거), 안전 계층 3중 설계, 통신 규약(WebSocket JSON, v2.8) 작성과 차량 클라이언트 구현, 비전 실행 프로세스를 맡았습니다.
  - 팀원 담당: Java 관제 서버·React 화면, ArUco 측위 서버, YOLO 세그먼테이션 모델
- **특징:**
  - 외부 연결 세 곳에 각각 대체 동작을 두어, 비전이 멈추면 GPS 단독으로 주행을 계속하고 관제가 끊기면 3초 안에 정지합니다. GPS 좌표가 끊기면 0.6초 뒤 정지하되, 유실이 잦은 지정 구역에서는 추측항법으로 1.2m까지 이어간 뒤 정지합니다.
  - 모든 구동 명령은 SafetySupervisor 한 곳을 거치고, 주행 프로세스 전체가 멈추면 별도 프로세스가 하트비트 파일을 감시해 0.7초 이상 갱신이 없을 때 I2C로 모터 전원을 차단합니다.
  - 조향이 지령각의 약 40%만 반영되는 편차는 원 주행으로 회전 반경을 실측해 수치화하고, 회전 제어를 실측 반경에 따른 고정 조향으로 바꿔 완주했습니다.
  - 테스트 383건과 통합 시뮬레이션(GPS 서버, 자체 관제 서버, 합성 카메라, 차량 전 스택을 실제 통신으로 연결)으로 실차 없이 PC에서 주행 시나리오를 검증했습니다.
- **한계:** 시연 스택에 비전 차선 보조가 포함됐으나 보정값의 부호·크기는 미검증 상태로 완주했고, 원인과 개선 방향을 저장소 README 트러블슈팅 절에 정리했습니다.
- **기술:** Python, WebSocket, Jetson Orin Nano, OpenCV(버드아이 변환), YOLO(TensorRT), PCA9685(I2C), pytest

| 구분 | 링크 |
| :--- | :--- |
| **GitHub (차량 파트 정리본)** | https://github.com/SJLee-83/autonomous-rc-taxi |

<br>

### 2. RC Car OTA: 무선 펌웨어 업데이트 + 원격 제어

> 차량을 무선으로 업데이트하고 원격 제어하는 SDV 개념을 RC카로 구현한 프로젝트로, 관제 GUI에서 MQTT로 명령을 보내면 ESP32 게이트웨이가 SPI로 차량 제어기(RA6E1)에 전달하고, 같은 경로로 펌웨어를 무선 배포합니다.

- **역할 (2인 팀):** RA6E1 주행 펌웨어(SPI Slave, I2C 모터·서보), 초음파 센서 기반 충돌 회피, 관제 GUI에서 MQTT와 ESP32를 거쳐 SPI로 이어지는 제어 체인 전체를 맡았습니다. 별도로 MCUboot 서명 검증 OTA를 독립 시도했습니다.
  - 팀원 담당: AI 음성 제어, Dual-Bank OTA(최종 시연 채택)
- **특징:**
  - SPI 자체에 흐름 제어가 없어, ESP32와 RA6E1 사이에 BUSY GPIO 핸드셰이크를 두어 슬레이브가 처리 중일 때 마스터가 다음 바이트를 보내지 못하게 했습니다.
  - 초음파 왕복시간은 타이머 채널을 쓰지 않고 DWT 사이클 카운터로 μs 단위 계측하며, 센싱은 비차단 상태머신으로 돌려 SPI 통신과 동시에 처리합니다.
  - 관제 GUI의 MQTT 콜백은 Signal/Slot으로 메인 스레드에 넘겨, 콜백 스레드가 위젯을 직접 건드려 발생하던 크래시를 없앴습니다.
- **한계:** MCUboot 작업 범위는 FSP 설정, 슬롯과 서명 파이프라인 구성, 실기 검증이며, 서명 검증·슬롯 교체·부팅까지는 실기로 확인했으나 무선 전송은 미완입니다. 실행 중인 코드 플래시에 쓰면 다음 명령을 fetch하지 못해 hang되는 self-programming 제약 때문이며, 팀이 채택한 Dual-Bank 방식은 이 제약을 하드웨어 뱅크 전환으로 우회합니다.
- **기술:** C, Renesas FSP(RA6E1 / Cortex-M33), ESP32(Arduino), MQTT, SPI, I2C, PySide6, MCUboot(ECDSA P-256)

| 구분 | 링크 |
| :--- | :--- |
| **GitHub (팀 전체)** | https://github.com/SJLee-83/rccar_ota_project |
| **GitHub (MCUboot 부트로더)** | https://github.com/SJLee-83/rccar-mcuboot-ota |

<br>

### 3. R-Mate: FANUC 설비 알람 조치 가이드 (RAG + LangGraph)

> 유지보수 매뉴얼 기반 RAG와 LangGraph 조건부 라우팅으로, 설비 알람 코드의 한국어 조치 가이드를 안전 주의사항과 출처를 붙여 생성합니다.

- **역할:** 스마트 제조 AI Agent 해커톤 2025(DACON) 본선 진출작(팀)의 기획과 MVP 설계를 맡았고(구현은 팀원 주도), 그 개념을 계승해 기술 스택과 데이터를 검증한 뒤 코드는 처음부터 단독으로 재구현했습니다.
- **특징:**
  - 기획 가정은 PoC로 검증한 뒤 통과한 것만 구현했습니다. 해커톤 기획의 핵심이던 "Cross-Reference RAG"는 매뉴얼 261페이지에서 `See Section` 참조가 3회에 그쳐 기각하고, 마커 기반 청킹과 메타데이터 필터링으로 재정의했습니다([ADR-002](https://github.com/SJLee-83/smart-mfg-ai-agent/blob/main/docs/decisions/ADR-002-cross-reference-rag-redefinition.md)).
  - 라우팅은 정규식 기반이고 LLM 호출은 답변 노드 한 곳뿐입니다. 필터 검색이 0건이면 필터를 풀고 1회 재검색하며, 그래도 0건이면 LLM을 호출하지 않고 종료합니다.
  - 단위·통합 테스트 63개를 두었고, 검색기와 LLM 클라이언트를 주입 가능하게 만들어 각 그래프 노드를 외부 호출 없이 검증합니다.
- **기술:** Python, LangGraph, Google Gemini, ChromaDB(로컬 벡터 DB)

| 구분 | 링크 |
| :--- | :--- |
| **GitHub (재개발, 단독)** | https://github.com/SJLee-83/smart-mfg-ai-agent |
| **GitHub (해커톤 원본, 팀원 계정)** | https://github.com/YuYeongChan/factory_doctor-fanuc_agent |

<br>

### 4. AI-SkinView: AI 챗봇 모듈 (팀 프로젝트)

> 피부 데이터(바우만 피부타입 설문 + 안면 분석 수치)를 바탕으로 맞춤 스킨케어 제품을 추천하는 대화형 챗봇입니다. 팀 프로젝트에서 AI 챗봇 모듈을 단독으로 담당했습니다.

- **역할 (6인 팀):** 챗봇 대화 상태 머신, RAG 기반 제품 추천(pgvector 코사인 거리 검색), 프리셋 저장을 맡았습니다. 모놀리식 `ChatDAO`를 컨트롤러/서비스/리포지토리/캐시 레이어드 아키텍처로 리팩터링했습니다.
  - 팀원 담당: 사용자 인증, YOLO 피부 분석, 바우만 설문, 카메라, 제품·루틴·마이페이지
- **특징:**
  - 대화 분기를 LLM 판단에 맡기면 대화가 길어질수록 의도를 잘못 파악해, 상태별 버튼으로 분기를 고정했습니다. 상태는 `initial_message` → `product_recommendation` → `product_usage` 세 단계이며, 대화 기록과 함께 Redis 세션(TTL 300초)에 보관합니다.
  - 의도 분류 LLM의 출력은 허용 목록 두 개(`제품 추천` · `단순 대화`)와 대조해 벗어나면 기본값으로 대체하며, 호출이 실패해도 같은 기본값으로 진행합니다.
  - 제품 검색은 pgvector `<=>` 코사인 거리 쿼리를 직접 작성했고, 피부 데이터와 질문을 임베딩해 제품 3종을 추출합니다.
- **한계:** 테스트가 없어, 리팩터링 중 상태 전이 키(`button_text`) 누락으로 2개 상태가 도달 불가해진 회귀 버그를 뒤늦게 발견해 수정했습니다(2026-07-12). 추천 품질 정량 지표도 없습니다.
- **기술:** FastAPI, PostgreSQL + pgvector, Redis, Azure OpenAI(gpt-4o-mini), React Native

| 구분 | 링크 |
| :--- | :--- |
| **개인 포트폴리오 (리팩터링 버전)** | https://github.com/SJLee-83/ai-skin-chatbot |
| **팀 프로젝트 원본** | https://github.com/SJLee-83/skinview-team-project |

<br>

### 5. Ant Insight: 투자 대시보드 (2인 팀)

> 환율·공포탐욕지수·주요 지수·종목 지표를 한 화면에 모아 보여주는 투자 대시보드입니다. 2인 팀에서 종목 상세 기능을 백엔드 서비스부터 프론트엔드까지 담당했습니다.

- **역할 (2인 팀):** 기술지표·실루엣 구간·AI 인사이트의 백엔드 서비스와 종목 상세 화면을 구현했습니다.
  - 팀원 담당: 홈 대시보드(환율·공포탐욕지수·지수), 투자 캘린더, 배포
- **특징:**
  - 지표 라이브러리(ta-lib) 없이 pandas·numpy로 이동평균(5/20/60/120일), 볼린저밴드(20일 2σ), RSI(Wilder 지수평활), 베타(공분산÷분산) 계산식을 작성했습니다.
  - 실루엣 5구간은 52주 고저 대비 현재가 위치를 5단계로 분류하고, 저점 구간인데 RSI가 과매수면 경고를 덧붙입니다.
  - 외부 데이터 수집은 live 실패 시 TTL이 지난 캐시, mock JSON 순으로 대체하고 그마저 없을 때만 503을 반환합니다. AI 인사이트는 Gemini 호출·파싱이 실패해도 mock으로 대체하며 AI 생성물 면책을 함께 표시합니다.
- **기술:** Python, FastAPI, pandas/numpy, pykrx/yfinance, Google Gemini, Next.js, TypeScript

| 구분 | 링크 |
| :--- | :--- |
| **GitHub (원본 팀 저장소, 기여 근거)** | https://github.com/domcastle/antproject |
| **GitHub (개인 정리본)** | https://github.com/SJLee-83/antproject |

<br>

### 6. CNN을 활용한 시력 보호 모델 설계 (졸업 종합설계)

> 웹캠으로 모니터와의 거리를 분류해, 너무 가까우면 경고로 눈의 피로를 줄이도록 돕는 CNN 모델입니다.

- **역할 (6인 팀):** 원본인 팀 졸업 프로젝트에서 학습 이미지 데이터 수집, 경고 화면 프론트엔드, 거리 분류 CNN 튜닝을 맡았습니다. 이 저장소는 개인 재구현본이며, FastAPI 백엔드와 전처리·학습·예측 모듈 분리는 재구현하며 직접 추가했습니다.
- **특징:** EfficientNetB0 전이학습(백본 동결), RetinaFace로 얼굴이 검출된 이미지만 남기는 학습 데이터 정제, ToF 센서로 실측한 거리를 파일명에 인코딩한 자동 라벨링.
- **성과:** 자체 테스트 분할 기준 분류 정확도 약 82%(단일 측정값이며 일반 사용 환경의 정확도를 보장하지는 않습니다).
- **기술:** Python, TensorFlow/Keras, OpenCV, FastAPI

| 구분 | 링크 |
| :--- | :--- |
| **GitHub** | https://github.com/SJLee-83/C-Care |
| **프로젝트 보고서 (PDF)** | https://github.com/SJLee-83/C-Care/blob/main/assets/C-Care-report.pdf |

<br>

## 학술 프로젝트

> 산업공학 전공으로 데이터 분석·통계·최적화를 적용한 학부 프로젝트입니다(분석·기획 중심).

| 프로젝트 주제 | 자료 |
| :--- | :--- |
| **행정복지센터 민원 시스템 최적화 제안**: 현장 실측(220건) → @Risk 분포 적합 → Simio 이산사건 시뮬레이션 → Paired-t 검증. 창구 재배치 시 대기시간 **−42.4%**(시뮬레이션 기준, p<0.001) | [보고서](https://github.com/SJLee-83/SJLee-83/raw/main/assets/죽전1동%20행정복지센터%20민원실%20대기시간%20감소%20및%20최적의%20민원%20시스템%20제안_보고서.pdf) |
| **담뱃값 인상에 따른 흡연율 및 판매량 예측 분석**: 회귀불연속(RDD) 인과 분석 | [보고서](https://github.com/SJLee-83/SJLee-83/raw/main/assets/담뱃값%20인상에%20따른%20흡연율%20차이%20분석%20및%20흡연율,%20담배%20판매량%20예측_보고서.pdf) ｜ [발표자료](https://github.com/SJLee-83/SJLee-83/raw/main/assets/담뱃값%20인상에%20따른%20흡연율%20차이%20분석%20및%20흡연율,%20담배%20판매량%20예측_ppt.pdf) |
| **살균기 데이터를 활용한 식품 살균 공정에서의 품질 예측**: 의사결정나무 튜닝으로 불량(NG) 탐지율 55% → 98%(테스트셋 기준). 트리의 분기 규칙을 현장 온도 조건으로 변환 | [발표자료](https://github.com/SJLee-83/SJLee-83/blob/main/assets/%EC%82%B4%EA%B7%A0%EA%B8%B0%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%A5%BC%20%ED%99%9C%EC%9A%A9%ED%95%9C%20%EC%8B%9D%ED%92%88%20%EC%82%B4%EA%B7%A0%20%EA%B3%B5%EC%A0%95%EC%97%90%EC%84%9C%EC%9D%98%20%ED%92%88%EC%A7%88%20%EC%98%88%EC%B8%A1.pdf) |
| **MFC 구축을 위한 입지 요건 분석**: 로지스틱 회귀 | [발표자료](https://github.com/SJLee-83/SJLee-83/blob/main/assets/GS%20%EC%B9%BC%ED%85%8D%EC%8A%A4%20MFC%EB%A5%BC%20%ED%99%9C%EC%9A%A9%ED%95%9C%2C%20MFC(Micro%20Fulfillment%20Center)%20%EA%B5%AC%EC%B6%95%EC%9D%84%20%EC%9C%84%ED%95%9C%20%EC%9E%85%EC%A7%80%20%EC%9A%94%EA%B1%B4%20%EB%B6%84%EC%84%9D.pdf) |
| **컨조인트 분석을 이용한 치킨 주문 행태 연구** | [보고서](https://github.com/SJLee-83/SJLee-83/raw/main/assets/컨조인트%20분석을%20이용한%20치킨%20주문%20이용실태%20및%20선택속성에%20대한%20연구_보고서.pdf) ｜ [발표자료](https://github.com/SJLee-83/SJLee-83/raw/main/assets/컨조인트%20분석을%20이용한%20치킨%20주문%20이용실태%20및%20선택속성에%20대한%20연구_ppt.pdf) |

<br>

## 연락처

- **Email:** greatsjlee@gmail.com
