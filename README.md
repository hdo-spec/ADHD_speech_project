# ADHD 자가진단 체크리스트 시스템

## 프로젝트 개요

본 프로젝트는 사용자의 ADHD 자가진단을 위한 음성인식 기반 **웹 시스템**이다.

사용자는 체크리스트 문항에 대해 선택 또는 음성(STT)으로 응답할 수 있으며, 결과는 점수화되어 제공된다.

특히 본 시스템은 다음과 같은 특징을 가진다:

* **TTS (Text-to-Speech)** : 문항을 음성으로 안내
* **STT (Speech-to-Text)** : 음성으로 답변 입력 가능
* **임시 저장 (Draft)** : 검사 도중 중단 후 이어서 진행 가능
* **CSV 기반 데이터 저장** : DB 없이 경량 구조 구현
* **관리자 기능** : 전체 응답 조회 및 삭제

---

## 기술 스택

### Frontend

* SvelteKit
* Vite
* JavaScript
* Web Speech API (STT)

### Backend

* FastAPI
* Python
* edge-tts (TTS)

### Data

* CSV 파일 기반 저장 (sessions, responses, drafts)

---

## 백엔드 구조

<pre class="overflow-visible! px-0!" data-start="717" data-end="807"><div class="relative w-full mt-4 mb-1"><div class=""><div class="relative"><div class="h-full min-h-0 min-w-0"><div class="h-full min-h-0 min-w-0"><div class="border border-token-border-light border-radius-3xl corner-superellipse/1.1 rounded-3xl"><div class="h-full w-full border-radius-3xl bg-token-bg-elevated-secondary corner-superellipse/1.1 overflow-clip rounded-3xl lxnfua_clipPathFallback"><div class="pointer-events-none absolute end-1.5 top-1 z-2 md:end-2 md:top-1"></div><div class="relative"><div class="pe-11 pt-3"><div class="relative z-0 flex max-w-full"><div id="code-block-viewer" dir="ltr" class="q9tKkq_viewer cm-editor z-10 light:cm-light dark:cm-light flex h-full w-full flex-col items-stretch ͼ5 ͼj"><div class="cm-scroller"><div class="cm-content q9tKkq_readonly"><span>backend/</span><br/><span>├── main.py</span><br/><span>├── routers/</span><br/><span>├── services/</span><br/><span>├── data/</span><br/><span>├── static/</span><br/><span>└── uploads/</span></div></div></div></div></div></div></div></div></div></div><div class=""><div class=""></div></div></div></div></div></pre>

### 구조 설명

* **main.py**
  * FastAPI 앱 생성
  * 라우터 연결
  * CORS 설정
  * static 파일 서빙 (TTS 음성)
* **routers/**
  * API 엔드포인트 정의 (Controller 역할)
  * questions / sessions / score / tts
* **services/**
  * 실제 로직 처리 (Business Logic)
  * CSV 처리, 점수 계산, TTS 생성 등
* **data/**
  * CSV 저장소 (DB 대체)
* **static/**
* 생성된 TTS mp3 파일 저장

---

## 프론트엔드 구조

<pre class="overflow-visible! px-0!" data-start="1155" data-end="1300"><div class="relative w-full mt-4 mb-1"><div class=""><div class="relative"><div class="h-full min-h-0 min-w-0"><div class="h-full min-h-0 min-w-0"><div class="border border-token-border-light border-radius-3xl corner-superellipse/1.1 rounded-3xl"><div class="h-full w-full border-radius-3xl bg-token-bg-elevated-secondary corner-superellipse/1.1 overflow-clip rounded-3xl lxnfua_clipPathFallback"><div class="pointer-events-none absolute end-1.5 top-1 z-2 md:end-2 md:top-1"></div><div class="relative"><div class="pe-11 pt-3"><div class="relative z-0 flex max-w-full"><div id="code-block-viewer" dir="ltr" class="q9tKkq_viewer cm-editor z-10 light:cm-light dark:cm-light flex h-full w-full flex-col items-stretch ͼ5 ͼj"><div class="cm-scroller"><div class="cm-content q9tKkq_readonly"><span>frontend/</span><br/><span>└── src/</span><br/><span>    ├── routes/</span><br/><span>    │   └── +page.svelte</span><br/><span>    ├── lib/</span><br/><span>    │   ├── components/</span><br/><span>    │   ├── api.js</span><br/><span>    │   └── stores.js</span></div></div></div></div></div></div></div></div></div></div><div class=""><div class=""></div></div></div></div></div></pre>

### 주요 컴포넌트

* SelectSession: 사용자 등록 및 선택
* Consent: 개인정보 동의
* Checklist: 검사 진행 (핵심 UI)
* Result: 결과 화면
* Admin: 관리자 데이터 조회
* AdminModal: 관리자 로그인

---

## 시스템 흐름

[사용자 등록]
   ↓
[개인정보 동의]
   ↓
[체크리스트 진행]
   ↓
[TTS 안내 + STT/선택 입력]
   ↓
[임시 저장]
   ↓
[제출]
   ↓
[점수 계산]
   ↓
[결과 출력]

---

## 주요 기능

### 1. 체크리스트 화면

* 카드형 UI
* 진행률 표시
* 자동 다음 이동
* 미답변 표시

---

### 2. STT 인식 방식

* Web Speech API 사용
* 인식된 텍스트 → 점수 변환

핵심 로직:

* 키워드 매칭
* 자모 분리
* 레벤슈타인 유사도 계산

→ 오타/발음 차이 허용하는 구조

---

### 3. TTS 동작 방식

* edge-tts 기반 음성 생성
* 해시 기반 파일 캐싱
* 재사용 가능 (중복 생성 방지)

---

### 4. 임시 저장 (Draft)

* answers를 JSON으로 직렬화
* drafts.csv에 저장
* 재접속 시 자동 복원

---

### 5. 관리자 기능

* 전체 응답 조회
* 문항별 답변 펼치기
* 선택 삭제
* 관리자 로그인 (하드코딩)

---

## CSV 파일 구조

### sessions.csv

| id | name | age | gender |

---

### responses.csv

| response_id | session_id | name | age | gender | total | recorded_at | q1~q20 |

---

### drafts.csv

| session_id | answers(JSON) | saved_at |

---

## 점수 계산 및 해석

* 각 문항: 0 ~ 3점
* 총점: 최대 60점

### 결과 해석

* 0 ~ 9점 → 정상 범위
* 10 ~ 18점 → 주의 필요
* 19점 이상 → 전문가 상담 권장

---

## 설치 필요 패키지

### Backend

pip install fastapi uvicorn edge-tts

### Frontend

npm install

---

## ▶ 백엔드 실행 방법

cd backend
uvicorn main:app --reload

기본 주소:

http://localhost:8000

---

## ▶ 프론트엔드 실행 방법

cd frontend
npm run dev

기본 주소:

http://localhost:5173

---

## ngrok 서버 실행 방법

외부에서 접속하기 위해 사용

ngrok http 5173

👉 생성된 URL로 외부 접속 가능

---

## 기타 주의 사항

* STT는 크롬 브라우저 권장
* 마이크 권한 필요
* edge-tts는 인터넷 연결 필요
* CSV 파일 인코딩: utf-8-sig
* 관리자 계정 (하드코딩)
* ID: admin
* PW: admin1234

---

## 설계 특징 (핵심 포인트)

* DB 대신 CSV 사용 → **빠른 개발 + 단순 구조**
* STT + TTS 결합 → **접근성 향상**
* Draft 기능 → **사용자 경험 개선**
* 유사도 기반 음성 인식 → **정확도 보완**
* FastAPI + SvelteKit → **경량 풀스택 구조**
