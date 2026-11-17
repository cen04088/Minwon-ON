# 🏛️ 민원ON (Minwon ON)

> **가까운 민원실이 아니라, 가장 빨리 끝나는 민원실을 추천합니다**

**🏆 2026 전국 통합데이터 활용 공모전 우수상 (전국 4위, 행정안전부)** · 개인 기획·개발·배포

전국 지자체 민원실의 실시간 대기 현황과 위치 기반 이동 시간을 더해, **이동 시간 + 대기 시간**이 가장 짧은 민원실을 추천하는 모바일 웹 서비스입니다.

기획·개발 과정: [브런치 글](https://brunch.co.kr/@e0636059d73b471/26)

## 한눈에 보기

| | |
|---|---|
| **문제** | 가장 가까운 민원실에 갔는데 대기가 길어 시간을 버리거나, 서류가 빠져 다시 방문한다 |
| **해결** | 실시간 번호표 대기 인원과 실제 주행 시간을 합산해 총 소요 시간이 가장 짧은 기관을 추천 |
| **데이터** | 공공데이터포털 민원실 실시간 대기현황·기본정보, Kakao Mobility 길찾기, Kakao Local 검색 |
| **AI** | Gemini가 "여권 발급할 때 뭐 필요해?" 같은 질문에 준비물을 안내해 서류 누락 재방문을 줄임 |
| **스택** | Django · Vanilla JS · PostgreSQL · Gemini API · PWA · Railway |

---

## ✨ 주요 기능

### 1. 두 가지 탐색 모드

- **⏱️ 실시간 예측 모드:** 실시간 대기 데이터를 제공하는 42개 시·구청(서울, 경기 등)의 번호표 대기 인원을 반영해 총 소요 시간을 계산합니다.
- **🌐 전국 일반 모드:** Kakao Local 검색으로 전국 행정복지센터와 시·구청을 찾습니다. 대기 데이터가 없는 지역에서는 이 모드로 자동 전환됩니다.

### 2. 🚗 실제 주행 시간 반영

- 직선 거리가 아니라 Kakao Mobility 길찾기 API의 **자동차 주행 시간**을 사용합니다.
- 도보·대중교통 소요 시간은 거리 기반으로 추정해 함께 보여 줍니다.
- 길찾기 API가 응답하지 않으면 거리 기반 추정값으로 대체하고, 결과는 5분간 캐시합니다.

### 3. 🤖 AI 민원 준비물 도우미 (Gemini)

- "여권 발급할 때 뭐 필요해?" 같은 자연어 질문에 필요한 서류를 안내합니다.
- 방문 전에 준비물을 확인해 **서류 누락으로 인한 재방문**을 줄입니다.

### 4. 📱 모바일 우선 · PWA

- 앱 설치 없이 웹에서 바로 쓰고, 홈 화면에 추가하면 앱처럼 열립니다.

---

## 📈 추천 알고리즘

거리순 정렬 대신 총 소요 시간으로 순위를 매깁니다.

$$ \text{총 소요 시간} = \text{이동 시간 (Kakao 길찾기)} + (\text{대기 인원} \times 3\text{분}) $$

- **1순위 (🏆 최단시간):** 총 소요 시간이 가장 짧은 기관
- **2순위 (✅ 차선책):** 대기 인원이 적어 안정적으로 처리할 수 있는 기관

대기 1명당 3분은 고정 가정값입니다. 기관별 주행 시간은 병렬로 조회해 응답 시간을 줄였습니다.

---

## 🛠️ 기술 스택

| 구분 | 사용 기술 |
|---|---|
| Backend | Python 3.10+, Django 4.2 |
| Frontend | Vanilla JavaScript (ES6+), CSS3 |
| AI/LLM | Google Gemini 2.5 Flash Lite (실패 시 1.5 Flash로 대체) |
| DB | SQLite (개발) / PostgreSQL (운영) |
| 외부 API | 공공데이터포털 (실시간 대기현황, 민원실 정보), Kakao Mobility Directions, Kakao Local Keyword Search |
| 배포 | Railway (`railway.toml`), PostgreSQL 자동 프로비저닝 |

---

## 📁 프로젝트 구조

```
Minwon-ON/
├── config/            # Django 프로젝트 설정
├── offices/           # 민원실 정보, 실시간 대기현황, 추천 알고리즘, AI 준비물 안내
├── templates/         # 화면 템플릿
├── static/            # css/js/PWA 매니페스트·서비스 워커
├── railway.toml       # 배포 설정
└── requirements.txt
```

---

## 🏗️ 설치 및 실행

1. **패키지 설치**
   ```bash
   pip install -r requirements.txt
   ```
2. **데이터베이스 마이그레이션**
   ```bash
   python manage.py migrate
   ```
3. **로컬 서버 실행**
   ```bash
   python manage.py runserver
   ```

### 환경 변수 (.env)

```ini
# Django
SECRET_KEY=your_secret_key
DEBUG=True

# 공공데이터 API (data.go.kr)
PUBLIC_DATA_API_KEY=your_key
WAITING_API_URL=실시간_대기현황_엔드포인트
OFFICE_INFO_API_URL=민원실_기본정보_엔드포인트

# Kakao Developers
KAKAO_REST_API_KEY=your_rest_api_key
KAKAO_JS_API_KEY=your_javascript_key

# Generative AI
GEMINI_API_KEY=your_gemini_key
```
