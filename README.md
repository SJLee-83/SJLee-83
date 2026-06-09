# 이승재 (Seung-Jae Lee)

산업공학에서 데이터 분석과 시스템 최적화를 공부하다 소프트웨어 개발로 영역을 넓혔습니다. RAG·LLM을 실제 제품에 적용하는 데 관심이 있고, 구현 전 기능들이 정상적으로 작동하는지 검증한 뒤 구현을 진행합니다.

<br>

## 기술 스택

주로 **Python · FastAPI**로 백엔드를, **React · Next.js · React Native**로 프론트엔드를 개발합니다. **RAG·LLM 응용**(LangGraph, Google Gemini, Azure OpenAI, pgvector)과 **CNN 비전**(TensorFlow, OpenCV)을 다뤘고, 데이터는 **PostgreSQL · Redis**를 사용합니다.

<br>

## 대표 프로젝트 (Featured Projects)

### 1. R-Mate — FANUC 설비 에러 진단 AI (RAG + LangGraph)

> 유지보수 매뉴얼 기반 RAG와 LangGraph 조건부 라우팅으로, 설비 알람 코드에 대한 **안전 우선 한국어 조치 가이드를 출처와 함께** 생성합니다.

- **역할:** DACON 스마트 제조 AI 해커톤 본선 진출작의 개념을 계승해 프로덕션 수준으로 **단독 재구현**
- **특징:** 기획 가정을 PoC로 먼저 검증하고 통과분만 구현하는 *검증 우선* 방식, 규칙 기반 라우팅 + 검색 0건 시 LLM 미호출 조기 종료, 단위·통합 테스트 61개. 정량 수치는 측정한 것만 기재(라우팅 25/25)
- **기술:** Python, LangGraph, Google Gemini, ChromaDB(로컬 벡터 DB)

| 구분 | 링크 |
| :--- | :--- |
| **💻 GitHub** | https://github.com/SJLee-83/smart-mfg-ai-agent |

<br>

### 2. AI-SkinView — AI 챗봇 모듈 (팀 프로젝트)

> 피부 데이터(설문 + 안면 분석)를 바탕으로 맞춤 스킨케어 제품을 추천하는 대화형 챗봇. 팀 프로젝트 중 **AI 챗봇 모듈을 단독으로 담당**했습니다.

- **역할:** 챗봇 대화 상태 머신, RAG 기반 제품 추천(pgvector 검색), 프리셋 저장, 프론트엔드~백엔드 전체. 모놀리식 구조를 컨트롤러/서비스/리포지토리/캐시 **레이어드 아키텍처로 리팩터링**
- **기술:** FastAPI, PostgreSQL + pgvector, Redis, Azure OpenAI, React Native

| 구분 | 링크 |
| :--- | :--- |
| **✨ 개인 포트폴리오 (리팩터링 버전)** | https://github.com/SJLee-83/ai-skin-chatbot |
| **👥 팀 프로젝트 원본** | https://github.com/SJLee-83/skinview-team-project |
| **📝 프로젝트 회고 (Notion)** | https://www.notion.so/AI-SkinView-239788271dd48086bfb6c5c3c777a138 |

<br>

### 3. Ant Insight — 투자 대시보드 (2인 팀)

> 환율·공포탐욕지수·주요 지수·종목 지표를 한 화면에 모아 보여주는 투자 대시보드. 2인 팀에서 **종목 상세 기능 전체(백엔드 서비스 ~ 프론트엔드)**를 담당했습니다.

- **역할:** 기술지표(MA·볼린저·RSI)·실루엣 구간·밸류에이션·수급·AI 인사이트의 백엔드 서비스와 종목 상세 화면을 **수직으로 구현**
- **특징:** RSI·볼린저를 HTS·TradingView와 같은 표준 계산식으로 구현, Gemini 인사이트(JSON 강제·실패 시 폴백·면책), 외부 데이터 폴백 처리
- **기술:** Python, FastAPI, pandas/numpy, pykrx/yfinance, Google Gemini, Next.js

| 구분 | 링크 |
| :--- | :--- |
| **💻 GitHub** | https://github.com/SJLee-83/antproject |

<br>

### 4. CNN을 활용한 시력 보호 모델 (졸업 프로젝트)

> 웹캠으로 모니터와의 거리를 분류해, 너무 가까우면 경고로 눈의 피로를 줄이도록 돕는 CNN 모델입니다.

- **역할:** 데이터 전처리(RetinaFace로 얼굴이 검출된 이미지만 선별), EfficientNetB0 전이학습, FastAPI + 웹 실시간 연동
- **성과:** 자체 테스트 분할 기준 분류 정확도 약 **82.8%**(단일 측정값)
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
| **컨조인트 분석을 이용한 치킨 주문 행태 연구** | [📄 보고서](https://github.com/SJLee-83/SJLee-83/raw/main/assets/컨조인트%20분석을%20이용한%20치킨%20주문%20이용실태%20및%20선택속성에%20대한%20연구_보고서.pdf) ｜ [📊 발표자료](https://github.com/SJLee-83/SJLee-83/raw/main/assets/컨조인트%20분석을%20이용한%20치킨%20주문%20이용실태%20및%20선택속성에%20대한%20연구_ppt.pdf) |
| **담뱃값 인상에 따른 흡연율 및 판매량 예측 분석** | [📄 보고서](https://github.com/SJLee-83/SJLee-83/raw/main/assets/담뱃값%20인상에%20따른%20흡연율%20차이%20분석%20및%20흡연율,%20담배%20판매량%20예측_보고서.pdf) ｜ [📊 발표자료](https://github.com/SJLee-83/SJLee-83/raw/main/assets/담뱃값%20인상에%20따른%20흡연율%20차이%20분석%20및%20흡연율,%20담배%20판매량%20예측_ppt.pdf) |
| **행정복지센터 민원 시스템 최적화 제안** | [📄 보고서](https://github.com/SJLee-83/SJLee-83/raw/main/assets/죽전1동%20행정복지센터%20민원실%20대기시간%20감소%20및%20최적의%20민원%20시스템%20제안_보고서.pdf) |
| **MFC 구축을 위한 입지 요건 분석** | [📊 발표자료](https://github.com/SJLee-83/SJLee-83/blob/main/assets/GS%20%EC%B9%BC%ED%85%8D%EC%8A%A4%20MFC%EB%A5%BC%20%ED%99%9C%EC%9A%A9%ED%95%9C%2C%20MFC(Micro%20Fulfillment%20Center)%20%EA%B5%AC%EC%B6%95%EC%9D%84%20%EC%9C%84%ED%95%9C%20%EC%9E%85%EC%A7%80%20%EC%9A%94%EA%B1%B4%20%EB%B6%84%EC%84%9D.pdf) |
| **살균기 데이터를 활용한 식품 살균 공정에서의 품질 예측** | [📊 발표자료](https://github.com/SJLee-83/SJLee-83/blob/main/assets/%EC%82%B4%EA%B7%A0%EA%B8%B0%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%A5%BC%20%ED%99%9C%EC%9A%A9%ED%95%9C%20%EC%8B%9D%ED%92%88%20%EC%82%B4%EA%B7%A0%20%EA%B3%B5%EC%A0%95%EC%97%90%EC%84%9C%EC%9D%98%20%ED%92%88%EC%A7%88%20%EC%98%88%EC%B8%A1.pdf) |
| **골목상권 되살리기 (FIELDCAMP)** | [📊 발표자료](https://github.com/SJLee-83/SJLee-83/blob/main/assets/%EA%B3%A8%EB%AA%A9%EC%83%81%EA%B6%8C%20%EB%90%98%EC%82%B4%EB%A6%AC%EA%B8%B0_FIELDCAMP_Project.pdf) |

<br>

## 연락처 (Contact)

- **Email:** greatsjlee@gmail.com
- **LinkedIn:** https://www.linkedin.com/in/seung-jae-lee-670baa382/
