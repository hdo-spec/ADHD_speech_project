# ADHD 자가진단 체크리스트 시스템

1. 시스템 개요

- 서비스 목적
  (ADHD 표준 설문(20문항)기반 자가진단 시스템)
- 주요 기능 요약
- 사용자 / 관리자 구분

서비스 목적
ADHD 표준 20문항 기반 자가진단 시스템
음성 인터랙션(TTS/STT)을 활용한 사용자 친화적 검사 환경 제공
간편한 검사 + 결과 제공을 통한 초기 증상 선별 지원
⚙️ 주요 기능 요약
사용자 등록 및 검사 진행
TTS/STT 기반 음성 인터랙션
자동 점수 계산 및 결과 제공
검사 데이터 저장 및 관리
관리자 응답 조회 및 관리 기능
👥 사용자 구성
👤 사용자 (일반 사용자)
대상자 등록 및 검사 수행
음성 또는 선택 기반 응답
결과 확인
🛠 관리자 (Admin)
전체 검사 데이터 조회
문항별 상세 응답 확인
데이터 삭제 및 관리

---

1. 기술 스택

- backend:

  ```
  Backend Architecture
  - FastAPI 기반 REST API 서버
  - Router / Service 계층 분리 구조 (Layered Architecture)

  API & Validation
  - RESTful API 설계 (HTTP Method 기반 자원 관리)
  - Pydantic을 활용한 요청 데이터 검증
  - 일관된 JSON 응답 구조 설계

  Data & Processing
  - CSV 기반 데이터 저장 (sessions / drafts / responses)
  - JSON 직렬화/역직렬화를 통한 임시 데이터 관리
  - UUID 기반 고유 식별자 생성
  - datetime을 활용한 데이터 기록 및 정렬

  Media & System
  - edge-tts 기반 음성 합성 기능 구현
  - 해시 기반 파일명 생성 (중복 방지)
  - TTS 결과 캐싱 (파일 재사용)
  - Static File Serving (음성 파일 제공)
  - CORS Middleware (프론트엔드 통신)
  ```

```
Backend Tech Stack
Framework
    FastAPI
Language
    Python
API
    RESTful API 설계
    JSON 기반 데이터 처리
Data Storage
    CSV 기반 데이터 관리
    (sessions, responses, drafts)
Core Features
    TTS 음성 파일 생성 API
    edge-tts 사용
    (gTTS 대비 음성 품질 및 속도 개선)
    응답 저장 및 점수 계산 로직
    임시저장 (Draft) 관리
    Core Features
Server
    Uvicorn
```

---

- frontend

```
🖥 Frontend Tech Stack
Framework
    SvelteKit 기반 SPA 구조
    컴포넌트 기반 UI 설계
Build Tool
    Vite
Language
    JavaScript (ES6+)
State Management
    Svelte Reactive System ($state, $derived, $effect)
API Communication
    REST API 기반 통신
    Fetch API (async/await)
Voice Technology
    Web Speech API (STT)
    TTS API (Backend - edge-tts)
    Web Audio API
    HTML5 Audio API
UI / UX
    SPA (Single Page Application)
    Custom CSS
    Component-Based Architecture

```




---

1. 핵심 기능 구현



# 주요 기능 (핵심 요약 1장)

## 1. 음성 기반 인터랙션 설계 (핵심 차별점)

* TTS + STT를 결합한 **양방향 음성 인터페이스 구현**
* 문항 음성 안내 → 사용자 음성 응답 → 자동 처리 흐름
* edge-tts 기반 **자연스러운 음성 + 속도 제어 지원**

👉 단순 설문이 아닌 “음성 기반 UX 시스템”으로 확장

---

## 2. STT 보정 알고리즘 (기술 포인트)

* 한글 자모 분리 + 레벤슈타인 거리 기반 유사도 계산
* 키워드 매칭 + 유사도 비교로 **오답 보정 처리**
* 음성 인식 오류를 고려한 **로버스트한 점수 변환 로직**

👉 “왜 이 로직을 만들었는지” 설명하면 평가 올라감

---

## 3. 실시간 데이터 처리 구조

* 선택 즉시 자동 저장 (Draft 시스템)
* 응답 → 점수 계산 → 저장까지 **비동기 처리**
* 중단 후 재접속 시 이어서 진행 가능

👉 사용자 경험 + 데이터 안정성 둘 다 잡은 설계

---

## 4. 관리자 데이터 관리 기능

* 전체 응답 조회 및 문항별 상세 확인
* 다중 선택 삭제 기능 (실제 운영 고려)
* 단순 결과 출력이 아닌 **데이터 관리 시스템 포함**

---

## 5. 구조 분리 설계 (아키텍처 완성도)

* Frontend / Backend / API 명확히 분리
* REST API 기반 통신 구조
* TTS, 데이터 저장, 로직 모듈화

👉 “확장 가능한 구조”라는 점 강조 가능

---

## 🔥 한 줄 핵심 (제일 중요)

👉 **“음성 인터랙션 + STT 보정 알고리즘 + 실시간 데이터 처리까지 고려한 사용자 중심 설계”**

---

이렇게 쓰면 좋은 이유:

* ❌ CRUD 앱처럼 안 보임
* ✅ “문제 해결 + 설계 고민 + 기술 적용”으로 보임
* ✅ 교수님 질문 포인트 자연스럽게 유도됨

---

## 1. 사용자 관리 기능

* 사용자(아동) 등록 / 조회 / 삭제
* 이전 검사 기록 존재 시 **팝업 안내 후 재검사 선택**
* 세션 기반 사용자 데이터 관리

---

## 2. 개인정보 동의 프로세스

* 검사 시작 전 **개인정보 수집 및 이용 동의 필수**
* 동의 여부에 따라 **검사 진행 제한 (UX 제어)**
* 사용자 정보와 검사 데이터 연동

---

## 3. 체크리스트 검사 시스템

* 총 20문항 기반 **ADHD 자가진단 체크리스트**
* 진행률 바 및 문항 네비게이션 제공
* 선택 즉시 자동 저장 + 자동 다음 문항 이동
* 미응답 문항 표시 및 이동 기능

---

## 4. 음성 기반 인터랙션 (핵심 기능)

* TTS: 문항 음성 안내 (속도 조절 가능)
* STT: 사용자 음성 답변 입력
* 음성 → 텍스트 → 점수 변환 자동 처리
* 버튼 클릭 없이도 **자연스러운 검사 흐름 구현**

---

## 5. STT 텍스트 처리 및 점수 매핑

* 한글 자모 분리 기반 텍스트 정규화
* 레벤슈타인 거리 기반 유사도 계산
* 키워드 + 유사도 매칭으로 **0~3점 자동 변환**
* 음성 인식 오류를 보완하는 로직 구현

---

## 6. 임시 저장 (Draft) 기능

* 문항 응답 진행 중 자동 저장
* 검사 중단 후 재접속 시 이어서 진행 가능
* 사용자별 draft 데이터 분리 관리

---

## 7. 결과 분석 및 피드백 제공

* 총점 기반 결과 산출
* 점수 구간별 맞춤 메시지 제공
* 정상 / 주의 / 전문가 상담 권장
* 직관적인 결과 UI 제공

---

## 8. 관리자 기능 (Admin Panel)

* 전체 사용자 응답 데이터 조회
* 문항별 상세 답변 펼치기 기능
* 체크박스 기반 **다중 선택 삭제**
* 데이터 관리 및 정리 기능 제공

---

## 9. 데이터 관리 구조

* CSV 기반 데이터 저장 (sessions / responses / drafts)
* REST API 통해 CRUD 처리
* 검사 기록 및 사용자 데이터 분리 관리

---

5. 트러블슈팅 및 개선

- 구현 중 어려웠던 점
- 해결 방법
- 성능 / UX 개선 포인트
- 향후 확장 방향


# 5. 트러블슈팅 및 개선

## 1. STT 인식 오류 문제

* **문제**
* 음성 인식 결과가 부정확 (예: “가끔” → “까끔”)
* 단순 문자열 비교 시 오답 처리 발생
* **해결**
* 한글 자모 분리 + 유사도(레벤슈타인 거리) 기반 매칭 로직 구현
* 키워드 + 유사도 혼합 방식으로 점수 변환
* **개선 효과**
* 음성 입력 정확도 향상
* 사용자 스트레스 감소 (UX 개선)

---

## 2. 검사 중 이탈 시 데이터 손실

* **문제**
* 새로고침 / 중단 시 응답 데이터 유실
* **해결**
* Draft API 기반 **임시 저장 기능 구현**
* 문항 선택 즉시 자동 저장 처리
* **개선 효과**
* 검사 중단 후 이어서 진행 가능
* 데이터 안정성 확보

---

## 3. TTS 품질 및 자연스러움 문제

* **문제**
* 기존 gTTS 음성이 부자연스럽고 지연 발생
* **해결**
* edge-tts로 변경 (Neural TTS)
* 재생 속도 조절 기능 추가
* **개선 효과**
* 자연스러운 음성 안내
* 사용자 몰입도 향상

---

## 4. 상태 관리 복잡도 증가

* **문제**
* 문항 이동, 음성 상태, 응답 상태 등 관리 복잡
* **해결**
* Svelte Reactive State ($state, $derived) 활용
* 상태 흐름 구조화 (currentIdx, answers 등)
* **개선 효과**
* 코드 가독성 향상
* 유지보수 용이성 증가

---

## 🚀 향후 확장 방향

* DB 기반 저장 구조로 확장 (CSV → RDB)
* 사용자별 맞춤형 검사 제공
* 결과 시각화 및 통계 분석 기능 추가
* AI 기반 음성 응답 해석 고도화

---

기타 설명

### 백엔드 기술 스택

- Python (Cpython)
  __pycache__ 기반 Python 3.11 실행 이력 확인 (개발 환경 기준)

  *“프로젝트에 버전 명시 파일은 없었지만,
  __pycache__의 cpython-311.pyc 파일을 통해
  Python 3.11 환경에서 실행된 이력은 확인했습니다.
  다만 정확한 런타임 버전은 명시적으로 관리되지는 않았습니다.”*

**1. 웹 프레임워크**

- FastAPI *메인 스택*
  - Python 기반 비동기 웹 API 프레임워크
  - 라우터 구조 지원

**2. API 구조 설계**

- Router 기반 모듈 분리 구조
- 기능별 API 분리(questions / sessions / score / tts)

routers/   → 요청/응답 (API 레이어)
services/  → 비즈니스 로직 (처리 로직)
data/      → 데이터 저장소

“API Layer - Service Layer 분리 구조”

🎯 아키텍처 명칭
Layered Architecture (계층형 구조)
또는
Controller-Service Pattern

👉 FastAPI 기준으로:

router = Controller 역할
service = Business Logic 역할

1️⃣ API 프레임워크 & 구조

- FastAPI
- APIRouter

👉 단순 사용이 아니라:

- prefix 기반 URL 설계
- 기능별 라우터 분리

**3. CORS 처리**

- CORSMiddleware
  - 프론트와 통신을 위한 설정

**4. 정적 파일 서빙**

- StaticFiles
  - tts 파일 서빙 담당

**5. Python 표준 라이브러리**

- pathlib

  - 파일 경로 관리 (OS 독립성)
- 데이터 처리:

  - CSV 파일 기반 데이터 저장 구조 설계

    - sessions: 사용자 정보 관리
    - drafts: 임시 저장 데이터 관리
    - responses: 최종 응답 데이터 저장
      **상태 데이터와 결과 데이터를 분리하여 관리**

    *“CSV를 단순 파일로 쓰기보다는
    sessions, drafts, responses로 나눠서
    각각 상태 / 임시 / 결과 데이터를 분리했습니다.
    이를 통해 데이터 흐름을 명확하게 관리하려고 했습니다.”*

요청/응답 데이터 검증
Pydantic
class SessionCreate(BaseModel):
    name: str
    age: int
    gender: str

👉 이건 꽤 중요한 포인트:

단순 dict 아님
요청 데이터 스키마 정의 + 검증
3️⃣ RESTful API 설계 (이건 확정 가능)

너 코드 보면:

@router.get("")
@router.post("")
@router.delete("/{session_id}")

👉 이건 명확하게:

자원: /sessions
행위: HTTP 메서드

✅ RESTful API 맞다고 말해도 됨

4️⃣ HTTP 응답 설계
return {"status": "success", "data": ...}

👉 여기서 뽑을 수 있는 것:

일관된 JSON 응답 구조 설계
상태값 관리 (success, error, none, found)
5️⃣ 파일 응답 처리 (TTS에서 핵심)
FileResponse
return FileResponse(str(path), media_type="audio/mpeg")

👉 이건 꽤 좋은 포인트:

단순 JSON API ❌
파일 스트리밍/서빙 API 구현

-services/
1️⃣ 데이터 처리 (핵심)
csv
json

👉 단순 파일 읽기가 아니라:

CSV → 구조화된 데이터 저장
JSON → 리스트 직렬화 (draft 저장)
json.dumps([...])
json.loads(...)

👉 이건 PPT에서:

“데이터 직렬화/역직렬화 처리”로 쓸 수 있음

2️⃣ 파일 시스템 제어
pathlib
Path(...).mkdir(parents=True, exist_ok=True)

👉 포인트:

OS 독립적 경로 처리
디렉토리 자동 생성
3️⃣ 고유 ID 생성
uuid
str(uuid.uuid4())[:8]

👉 이건 꽤 좋음:

단순 index ❌
고유 식별자 기반 데이터 관리
4️⃣ 데이터 처리 로직 (비즈니스 로직)
total = sum(a.value for a in answers)

👉 이건 기술이라기보다:

도메인 로직 구현 (점수 계산)
5️⃣ 시간 처리
datetime
datetime.now().strftime(...)

👉 포인트:

기록 시간 관리
최신 데이터 정렬
6️⃣ 🔥 TTS (가장 강력한 차별화)
edge_tts
edge_tts.Communicate(text, voice="ko-KR-SunHiNeural")

👉 이건 진짜 중요:

단순 기능 ❌
외부 음성 합성 엔진 활용
7️⃣ 캐싱 전략 (은근 핵심 포인트)
if not path.exists():
    tts.save_sync(...)

👉 이거 진짜 좋다:

같은 텍스트 → 재생성 안 함
파일 재사용

👉 PPT에서 이렇게 표현 가능:

“TTS 결과 캐싱을 통한 불필요한 재생성 방지”

8️⃣ 해시 기반 파일명 생성
hashlib
hashlib.md5(...)

👉 포인트:

텍스트 기반 고유 파일명 생성
충돌 방지 + 재사용 가능

---

프레임워크
Svelte
SvelteKit

👉 +page.svelte 구조 = SvelteKit 확정

2️⃣ 상태 관리 (이거 핵심 포인트 ⭐)
let phase = $state('select');
let unanswered = $derived(...)
$effect(...)

👉 이건:

Svelte reactive state 시스템 활용

👉 PPT 표현:

“Reactive State 기반 UI 상태 관리”

3️⃣ API 통신
import { getSessions, getQuestions, submitAnswers } from '$lib/api.js';

👉 이건:

백엔드(FastAPI)와 통신

👉 PPT:

“REST API 기반 클라이언트-서버 통신”

4️⃣ 음성 기능 (🔥 핵심 차별화)
🎤 STT
Web Speech API
window.SpeechRecognition || window.webkitSpeechRecognition

👉 기능:

음성 → 텍스트 변환
🔊 TTS
서버 TTS + Audio 재생
new Audio("http://localhost:8000/api/tts/file/...")

👉 구조:

프론트: 재생
백엔드: 생성

👉 이건 풀스택 연동 포인트

5️⃣ 오디오 제어
audio.playbackRate = ttsSpeed;

👉 포인트:

속도 제어
재생 상태 관리
6️⃣ UX 로직 (이거 진짜 좋음)
phase = 'select' → 'consent' → 'checklist' → 'result'

👉 이건:

단순 페이지 이동 ❌
상태 기반 화면 전환

👉 PPT:

“State-driven UI Flow 설계”

7️⃣ 비동기 처리
async/await
onMount(async () => {...})

👉 기본이지만 필수:

API 호출
음성 처리 흐름 제어
8️⃣ 사용자 경험 (은근 강점)
자동 TTS → STT 이어짐
무음 감지 → 다시 요청
자동 다음 문항 이동

👉 이건 그냥 기능이 아니라:

인터랙션 설계 능력

gtts-> edgetts
“처음에는 gTTS를 사용했는데, 음성이 다소 기계적이고 응답 속도가 느린 문제가 있어서 edge-tts로 변경했습니다.”

“edge-tts는 Microsoft의 Neural TTS 기반이라 음성이 훨씬 자연스럽고 사람 목소리에 가깝습니다.”

“또한 생성 속도도 더 빨라서, 사용자에게 음성이 재생되기까지의 지연 시간이 줄어들어 전체적인 사용자 경험이 개선되었습니다.”

“특히 이 프로젝트는 음성 인터랙션이 핵심이기 때문에, 음질과 응답 속도를 개선하는 것이 중요하다고 판단해서 교체했습니다.”


Q1. 왜 레벤슈타인 거리(Levenshtein Distance)를 사용했나요?

답변 핵심 구조 (이대로 말하면 됨)
👉 “STT 특성 + 문제 상황 → 선택 이유 → 결과”

💬 답변

음성 인식(STT)은 발음이나 잡음에 따라 단어가 조금씩 틀리게 인식되는 문제가 있습니다.
예를 들어 ‘가끔’이 ‘까끔’처럼 유사하지만 다른 문자열로 들어오는 경우가 많습니다.

이런 상황에서 단순 문자열 비교는 정확하게 매칭이 어렵기 때문에,
문자열 간의 유사도를 계산할 수 있는 레벤슈타인 거리를 사용했습니다.

이를 통해 완전히 일치하지 않더라도 일정 수준 이상 유사하면 같은 의미로 처리할 수 있도록 구현했습니다.

❓ Q2. 왜 자모 분리까지 했나요?

💬 답변

한글은 음절 단위로 비교하면 작은 발음 차이도 완전히 다른 문자로 인식됩니다.

그래서 ‘가끔’과 ‘까끔’을 비교할 때,
자모 단위(ㄱㅏㄲㅡㅁ)로 분리하면 더 세밀한 비교가 가능해집니다.

결과적으로 STT 오차에 더 강한 매칭을 구현할 수 있었습니다.

👉 핵심: “한글 특성 고려했다” 강조

❓ Q3. 다른 방법은 고려 안 했나요?

💬 답변

처음에는 키워드 완전 일치 방식만 사용했는데,
STT 오차로 인해 인식률이 낮아지는 문제가 있었습니다.

이후 더 복잡한 NLP 모델도 고려할 수 있었지만,
현재 시스템은 4지선다 구조이기 때문에
가볍고 빠르게 적용 가능한 유사도 기반 방식이 더 적합하다고 판단했습니다.

👉 핵심:

“비교했다”
“의도적으로 선택했다”
❓ Q4. 성능 문제는 없었나요?

💬 답변

레벤슈타인 거리 계산은 문자열 길이가 짧고
비교 대상이 제한적이기 때문에 성능 부담은 크지 않았습니다.

또한 키워드 매칭을 먼저 수행하고,
그 다음에 유사도 계산을 적용하는 방식으로 최적화했습니다.

👉 핵심:

무조건 “성능 괜찮습니다” ❌
“왜 괜찮은지” 설명
❓ Q5. 이 방식의 한계는?

💬 답변

유사도 기반 방식은 의미를 완전히 이해하는 것은 아니기 때문에
문맥이 다른 경우에도 잘못 매칭될 가능성이 있습니다.

향후에는 AI 기반 자연어 처리 모델을 적용하여
의미 기반 해석으로 확장할 수 있다고 생각합니다.

👉 이거 말하면 교수님 좋아함 (확장성)

🔥 한 방 요약 (마지막 멘트)

👉 “STT의 불완전성을 보완하기 위해, 한글 구조를 고려한 유사도 기반 매칭을 적용했습니다.”