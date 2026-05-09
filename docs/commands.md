# 명령어 레퍼런스

> gstack v1.29.0.0 기준 47개 명령어 상세 가이드

[← README로 돌아가기](../README.md)

---

## 1. 기획 (Think & Plan)

### `/office-hours` — YC 오피스 아워

**역할**: 제품 아이디어를 재구성하는 6가지 강제 질문으로 코드 작성 전에 문제를 재정의한다.

**기능**:
- 전제를 도전하고, 프레이밍을 뒤집음
- 구현 대안을 생성하고 노력 추정치 제공
- 디자인 문서를 자동 작성하여 다운스트림 스킬에 전달

**사용 예시**:
```
You:    일일 브리핑 앱을 만들고 싶어
You:    /office-hours

Claude: [고통점에 대한 구체적인 질문을 던짐]

You:    여러 Google 캘린더를 쓰는데, 이벤트 정보가 오래되고
        장소도 틀린 경우가 많아. 준비에 시간이 너무 걸려.

Claude: 프레이밍을 바꿔보겠습니다. "일일 브리핑 앱"이라고 하셨지만
        실제로 설명하신 건 "개인 비서 AI"입니다.
        [미처 인식하지 못한 5가지 기능을 추출]
        [4가지 전제에 도전]
        [3가지 구현 접근법 + 노력 추정치]
        추천: 내일 당장 가장 좁은 쐐기를 출시하세요.
        전체 비전은 3개월 프로젝트입니다.
        [디자인 문서 작성 → 다운스트림 스킬에 자동 전달]
```

### `/plan-ceo-review` — CEO / 파운더

**역할**: 문제를 사용자 관점에서 재사고하고, 요청 안에 숨겨진 10점짜리 제품을 찾아낸다.

**4가지 모드**:
- **Expansion**: 스코프 확대
- **Selective Expansion**: 선택적 확대
- **Hold Scope**: 현재 유지
- **Reduction**: 스코프 축소

**사용 예시**:
```
You:    /plan-ceo-review
Claude: [디자인 문서를 읽고 10개 섹션으로 리뷰 수행]
        이 기능은 진짜 맞는 제품인가요?
        [스코프 도전, 사용자 가치 평가, 우선순위 제안]
```

### `/plan-eng-review` — 엔지니어링 매니저

**역할**: 아키텍처, 데이터 플로우, 상태 전이, 엣지 케이스, 실패 모드, 테스트 커버리지를 확정한다.

**기능**:
- ASCII 다이어그램 생성 (시퀀스, 상태, 컴포넌트, 데이터 플로우)
- 숨겨진 가정을 다이어그램으로 드러냄
- 테스트 매트릭스 작성

**사용 예시**:
```
You:    /plan-eng-review
Claude: [데이터 플로우, 상태 머신, 에러 경로를 ASCII 다이어그램으로 생성]
        [테스트 매트릭스, 실패 모드, 보안 우려사항 정리]
```

### `/plan-design-review` — 시니어 디자이너

**역할**: 각 디자인 차원을 0~10으로 평가하고, 10점이 어떤 모습인지 설명한 뒤, 계획을 편집하여 10점에 도달하게 한다.

**기능**:
- AI Slop 탐지 (AI가 만들어낸 저품질 디자인 감지)
- 인터랙티브: 디자인 선택마다 사용자에게 질문

### `/plan-devex-review` — DX 시니어 (v1.27.0.0+)

**역할**: 인터랙티브 개발자 경험 플랜 리뷰. 개발자 페르소나 탐색, 경쟁 벤치마크, 마법 같은 순간(magical moments) 설계, 마찰 지점 추적 후 점수 부여.

**3가지 모드**:
- **DX EXPANSION**: 경쟁 우위 만들기
- **DX POLISH**: 모든 터치포인트 강화
- **DX TRIAGE**: 결정적 갭만 처리

**언제 쓰나**: 개발자 대면 제품(API, CLI, SDK, 라이브러리, 플랫폼, 문서)의 플랜이 있을 때

### `/plan-tune` — 질문 민감도 자기 튜닝 (v0.19.0.0+)

**역할**: gstack이 어떤 프롬프트를 가치 있게 여기는지, 어떤 게 노이즈인지 학습한다. 같은 `AskUserQuestion`에 매번 같은 답을 하면 이 스킬이 gstack에게 그만 묻도록 가르친다.

**기능**:
- 질문별 선호도 설정: never-ask / always-ask / ask-only-for-one-way
- 듀얼 트랙 빌더 프로필: 자기 진단(5차원) vs 행동 기반 추적
- 8개 빌더 archetypes: Cathedral Builder, Ship-It Pragmatist, Deep Craft, Taste Maker, Solo Operator, Consultant, Wedge Hunter, Builder-Coach
- 일방통행 도어(파괴적 작업, 아키텍처 분기, 보안 선택)는 항상 묻는다 (안전이 선호도를 이김)

**활성화**:
```bash
gstack-config set question_tuning true
```

비활성화 상태에서는 0 영향. 인라인 `tune:` 피드백은 사용자 본인 채팅에서 온 prefix만 수용 (프로필 포이즈닝 방어).

### `/design-consultation` — 디자인 파트너

**역할**: 처음부터 완전한 디자인 시스템을 구축한다.

**기능**:
- 경쟁 제품 리서치
- 안전한 선택과 창의적 리스크를 동시 제안
- 실제 제품의 현실적 목업 생성
- `DESIGN.md` 작성 → `/design-review`와 `/plan-eng-review`에 자동 전달

### `/autoplan` — 자동 리뷰 파이프라인

**역할**: 한 명령으로 CEO → 디자인 → 엔지니어링 → DX 리뷰를 자동 수행한다.

**기능**:
- 6가지 의사결정 원칙을 인코딩하여 자동 리뷰
- 테이스트 결정(사람의 판단이 필요한 부분)만 사용자에게 질문
- close approaches, borderline scope, codex 불일치 등을 표면화
- v1.27.0.0+: DX 리뷰 통합. 개발자 대면 제품이면 `/plan-devex-review`도 자동 포함

---

## 2. 디자인 (Design)

### `/design-shotgun` — 디자인 탐색

**역할**: 여러 AI 디자인 변형을 생성하고, 비교 보드를 열어 구조화된 피드백을 수집한 뒤 반복 개선한다.

**기능**:
- 다수의 AI 디자인 변형 동시 생성
- 비교 보드에서 나란히 비교 (평가 컨트롤, 댓글, 리믹스/재생성 버튼 포함)
- 구조화된 피드백 수집 (`feedback.json`으로 자동 수집)
- 선택한 방향으로 반복 개선
- `/design-html`로 바로 이어서 프로덕션 코드 변환 가능

**v0.14.1.0 변경: 비교 보드가 기본 선택기**:
- 디자인 변형 생성 후 비교 보드가 자동으로 열림
- 인라인 이미지 + "어떤 게 좋아요?" 방식 대신, 보드에서 평가·댓글·피드백을 직접 수행
- 보드 서버 실패 시 인라인 폴백으로 자동 전환

**사용 예시**:
```
You:    /design-shotgun
Claude: [4가지 디자인 변형 생성]
        비교 보드를 열었습니다: http://127.0.0.1:3847/
        보드에서 평가하고 Submit을 클릭하세요.
        [피드백 수신 → 선택한 방향으로 개선]
```

### `/design-html` — 디자인 → 프로덕션 코드

**역할**: 어떤 출발점에서든 프로덕션 품질의 HTML/CSS를 생성한다. `/design-shotgun` 없이도 독립 실행 가능.

**v0.15.1.0 변경: 독립 실행 가능**:
- 세 가지 라우팅 모드: (A) `/design-shotgun`에서 승인된 목업, (B) CEO 플랜이나 디자인 변형이 있지만 공식 승인 없음, (C) 설명이나 PNG만으로 처음부터 디자인
- "승인된 디자인이 없습니다" 대신, 어떻게 진행할지 선택지를 제공
- 플랜, 설명, 또는 제공된 PNG에서 바로 시작 가능

**기능**:
- 텍스트가 실제로 리플로우되는 HTML 생성 (고정 높이 아님)
- 동적 레이아웃, 계산된 높이
- 30KB 오버헤드, 외부 의존성 없음
- 스마트 API 라우팅으로 최적의 AI 모델 선택

---

## 3. 리뷰 (Review)

### `/review` — 스태프 엔지니어

**역할**: CI를 통과하지만 프로덕션에서 터지는 버그를 찾아낸다. 7종의 전문 리뷰어가 병렬로 동작한다.

**v0.14.4.0 신규: 병렬 전문 리뷰어**:
- **항상 활성화**: 테스트 갭 + 유지보수성 리뷰어
- **조건부 활성화**: 보안 (auth 스코프), 성능 (백엔드/프론트엔드), 데이터 마이그레이션, API 계약, Red Team (200줄 이상 diff)
- 각 리뷰어가 독립적으로 diff를 읽고, 구조화된 JSON으로 결과 출력
- 같은 file:line:category를 2명 이상이 지적하면 신뢰도 상승 ("MULTI-SPECIALIST CONFIRMED")
- **PR 품질 점수**: 0~10 점수 산출, `/retro`에서 추세 확인 가능

**v0.14.3.0 신규: 항상 활성화된 적대적 리뷰**:
- 모든 리뷰에서 Claude 적대적 서브에이전트 + Codex 적대적 챌린지가 자동 실행
- diff 크기와 관계없이 동일한 크로스 모델 보안 분석 적용

**탐지 대상**:
- N+1 쿼리
- 레이스 컨디션
- 트러스트 바운더리 위반
- 누락된 인덱스
- 잘못된 재시도 로직
- 스테일 읽기

**동작 방식**:
- **AUTO-FIX**: 포매팅, 임포트, 네이밍 등 명백한 문제는 자동 수정
- **ASK**: 레이스 컨디션 등 판단이 필요한 문제는 사용자에게 확인

**사용 예시**:
```
You:    /review

Claude: [7 specialists dispatched in parallel]
        ✅ Testing specialist: 2 gaps found
        ✅ Security specialist: clean
        ✅ Performance specialist: N+1 query detected
        [AUTO-FIXED] 2개 이슈: import 정렬, 미사용 변수 제거
        [ASK] services/auth.ts:47 — 레이스 컨디션 발견.
        동시 로그인 시 세션 데이터가 덮어씌워질 수 있습니다.
        수정하시겠습니까? (Y/N)
        PR Quality Score: 8.5/10
```

### `/investigate` — 디버거

**역할**: 체계적 근본 원인 디버깅. "조사 없는 수정은 없다"는 철칙을 따른다.

**규칙**:
- 4단계 프로세스: 조사 → 분석 → 가설 → 구현
- 데이터 플로우 추적
- 가설 테스트
- 3회 수정 실패 시 중단
- 조사 대상 모듈에 자동 freeze 적용

### `/codex` — 세컨드 오피니언

**역할**: OpenAI Codex CLI를 통한 독립적 코드 리뷰.

**3가지 모드**:
- **review**: 통과/불합격 게이트
- **adversarial challenge**: 적대적 도전 (코드를 깨려고 시도)
- **open consultation**: 자유 상담 (세션 연속성 지원)
- `/review`와 `/codex` 모두 실행 시 크로스 모델 분석 가능

**v0.14.3.0 변경**: `/review` 실행 시 Codex 적대적 챌린지가 자동으로 함께 실행됨. 별도로 `/codex`를 실행하지 않아도 크로스 모델 보안 분석이 적용된다. `codex_reviews=disabled` 설정은 Codex 패스만 비활성화하고, Claude 적대적 서브에이전트는 항상 실행된다.

### `/devex-review` — 라이브 DX 감사 (v1.27.0.0+)

**역할**: `browse` 도구로 실제 개발자 경험을 테스트한다. 문서를 탐색하고, getting started 플로우를 시도하고, TTHW(Time-To-Hello-World)를 측정하고, 에러 메시지 스크린샷을 찍고, CLI 헬프 텍스트를 평가한다. 증거 기반 DX 스코어카드 생성.

**기능**:
- 문서 사이트 라이브 탐색
- 첫 hello world까지의 시간 측정
- CLI/API 에러 메시지 캡처
- `/plan-devex-review` 점수와 비교 (부메랑 효과: 플랜은 3분이라 했는데 실제는 8분)

**언제 쓰나**: 개발자 대면 기능을 출하한 직후. 출하 후에 자동 제안된다.

---

## 4. 테스트 & QA

### `/qa` — QA 리드

**역할**: 앱을 테스트하고, 버그를 찾고, 원자적 커밋으로 수정하고, 재검증한다.

**동작 방식**:
1. `git diff`를 읽고 변경이 영향을 미치는 페이지/라우트 식별
2. 실제 브라우저를 열어 각각 테스트
3. 발견한 버그를 원자적 커밋으로 수정
4. 수정마다 회귀 테스트 자동 생성
5. 재검증

**사용 예시**:
```
You:    /qa https://staging.myapp.com

Claude: [실제 Chromium 브라우저를 열고 테스트]
        ✅ 로그인 플로우 — 정상
        ✅ 대시보드 로딩 — 정상
        ❌ 프로필 수정 — "저장" 버튼 클릭 시 500 에러
        [원인 분석 → 수정 커밋 → 회귀 테스트 생성 → 재검증]
        ✅ 프로필 수정 — 수정 후 정상
```

### `/qa-only` — QA 리포터

**역할**: `/qa`와 동일한 방법론이지만 **코드 수정 없이 버그 리포트만** 생성한다. 수정 권한 없이 보고만 필요할 때 사용.

### `/design-review` — 디자이너 + 코더

**역할**: `/plan-design-review`와 동일한 감사를 수행한 뒤, 발견한 문제를 직접 수정한다.

**기능**:
- 시각적 불일치, 간격 문제, 계층 구조 문제, AI slop 패턴 탐지
- 원자적 커밋으로 수정
- 수정 전/후 스크린샷 생성으로 비교

### `/benchmark` — 퍼포먼스 엔지니어

**역할**: 페이지 로드 타임, Core Web Vitals, 리소스 사이즈를 측정하고, PR마다 전/후 비교를 수행한다.

**측정 항목**:
- 페이지 로드 타임
- Core Web Vitals (LCP, FID, CLS)
- 리소스 사이즈
- 시간에 따른 성능 추세 추적

### `/benchmark-models` — 크로스 모델 벤치마크 (신규)

**역할**: 같은 프롬프트를 Claude, GPT(Codex CLI 경유), Gemini에 동시에 돌리고 비교한다. "이 스킬에 어떤 모델이 진짜 제일 좋나?"를 데이터로 답한다 (vibes 말고).

**측정 항목**:
- 지연(latency) 비교
- 토큰 소비량
- 비용
- 선택적: LLM 저지로 품질 평가

**`/benchmark`와 차이**: `/benchmark`는 웹 페이지 성능. `/benchmark-models`는 모델 성능.

---

## 5. 배포 & 릴리스 (Ship)

### `/ship` — 릴리스 엔지니어

**역할**: 메인 브랜치 동기화, 테스트 실행, 스코프 드리프트 감지, 푸시, PR 생성을 한 명령으로 처리한다.

**기능**:
- 베이스 브랜치 감지 및 머지
- 전체 테스트 실행
- 테스트 프레임워크가 없으면 자동 부트스트랩
- diff 리뷰
- VERSION 범프, CHANGELOG 업데이트
- PR 생성

**v0.14.5.0 신규: 멱등성 보장**:
- 푸시 성공 후 PR 생성 실패 시 재실행해도 VERSION 이중 범프나 CHANGELOG 중복 없음
- 이미 범프된 VERSION 감지, 이미 올린 코드 스킵, 기존 PR 바디 업데이트

**v0.14.3.0 신규: 스코프 드리프트 감지**:
- 배포 전에 "계획한 것만 만들었는지" 자동 검증
- 스코프 크리프("이 파일 수정하는 김에..." 변경)와 누락된 요구사항 감지
- 결과는 PR 바디에 포함

**사용 예시**:
```
You:    /ship

Claude: ✅ main 브랜치 동기화 완료
        ✅ 테스트 실행: 42 → 51 (+9 신규)
        ✅ 커버리지: 82% → 85%
        ✅ 스코프 드리프트: clean (계획과 구현 일치)
        ✅ PR 생성: github.com/you/app/pull/42
```

### `/land-and-deploy` — 릴리스 엔지니어

**역할**: PR 머지 → CI 대기 → 배포 → 프로덕션 헬스 체크를 한 명령으로 처리한다.

"승인됨"에서 "프로덕션 검증 완료"까지 한 번에.

### `/canary` — SRE

**역할**: 배포 후 모니터링 루프. 콘솔 에러, 성능 회귀, 페이지 실패를 감시한다.

**기능**:
- 주기적 스크린샷 캡처
- 배포 전 베이스라인과 비교
- 이상 감지 시 즉시 알림

### `/document-release` — 테크니컬 라이터

**역할**: 방금 배포한 내용에 맞게 프로젝트의 모든 문서를 업데이트한다. 오래된 README를 자동 감지한다.

**기능**:
- 코드 diff와 문서 비교
- 팩트 업데이트는 자동 수정
- 판단이 필요한 변경은 사용자에게 질문
- CHANGELOG 보이스 폴리싱
- 크로스 문서 일관성 검증

### `/landing-report` — 랜딩 리포트 (신규)

**역할**: 워크스페이스 인식 PR 큐 대시보드. 어떤 VERSION 슬롯이 현재 열린 PR로 클레임됐는지, 어떤 sibling Conductor 워크스페이스에 곧 출하될 WIP가 있는지, `/ship`이 다음에 어떤 슬롯을 선택할지 보여준다.

**특징**: 읽기 전용. 변경 없음. 큐 스냅샷만.

**언제 쓰나**: "지금 큐에 뭐 있지?", "다음에 내가 어떤 버전을 클레임하지?"

---

## 6. 회고 (Reflect)

### `/retro` — 엔지니어링 매니저

**역할**: 팀 인식 주간 회고.

**기능**:
- 기여자별 분석 (라인 수, 커밋 수, 테스트 건강도)
- 배포 연속기록 추적
- 성장 기회 제안
- `/retro global`: 모든 프로젝트와 AI 도구(Claude Code, Codex, Gemini)를 아우르는 글로벌 회고

---

## 7. 브라우저 자동화

### `/browse` — QA 엔지니어

**역할**: 실제 Chromium 브라우저로 클릭, 네비게이션, 스크린샷을 수행한다.

**특징**:
- 초기 시작 후 명령당 ~100ms 응답
- 쿠키, 탭, localStorage가 명령 간 유지
- 30분 유휴 시 자동 종료
- CSS 셀렉터 대신 접근성 트리 참조(@e1, @e2) 사용
- localhost 전용 바인딩, Bearer 토큰 인증
- 75개 명령 surface (47개 스킬과 함께 `gstack/llms.txt`에 자동 인덱싱)

**v1.28.0.0 신규: 진짜 자동화 능력**:
- **`browse --proxy <url>`**: SOCKS5(인증 포함), HTTP, HTTPS 프록시. 임베디드 SOCKS5 브릿지로 Chromium이 인증 업스트림 사용 가능
- **`browse --headed` Linux 자동 Xvfb**: DISPLAY 없는 컨테이너에서 첫 빈 디스플레이 자동 spawn
- **`download --navigate`**: 브라우저 native 다운로드 핸들러. Content-Disposition 헤더, 멀티홉 CDN 리다이렉트, 안티봇 CDN 체인 처리
- **stealth 축소**: `navigator.webdriver` 마스킹만 유지. plugins/languages 가짜 값은 봇 감지를 도와서 제거

**사용 예시**:
```
You:    /browse goto https://example.com
Claude: [페이지 로딩, 접근성 트리 반환]

You:    /browse snapshot
Claude: [현재 페이지 스크린샷 캡처]
```

### `/open-gstack-browser` — GStack Browser (신규)

**역할**: AI 제어 Chromium을 사이드바 확장 내장 상태로 실행. 모든 동작을 실시간으로 볼 수 있는 가시 브라우저 창. 사이드바가 라이브 활동 피드와 채팅을 보여준다. 안티봇 stealth 내장.

**기능**:
- 한 명령으로 실제 Chrome 창 시작
- 사이드바에서 모든 동작 실시간 확인
- CSS Inspector: 요소 클릭으로 CSS 규칙 캐스케이드, specificity 배지, 박스 모델 시각화
- 라이브 스타일 편집: `$B style .selector property value`로 실시간 CSS 수정
- 탭별 에이전트: 탭마다 독립 에이전트 프로세스
- LLM 기반 페이지 클린업

**이전 `/connect-chrome`에서 확장되어 별도 스킬로 분리된 상위 호환.**

### `/pair-agent` — 에이전트 페어링 (신규)

**역할**: 다른 AI 에이전트(OpenClaw, Hermes, Codex, Cursor 등 HTTP 요청 가능한 모든 에이전트)를 너의 브라우저와 페어링한다. 한 명령으로 setup 키 생성, 다른 에이전트가 따라갈 지침 출력. 원격 에이전트는 자체 탭에 스코프 권한(기본 read+write, 요청 시 admin) 받음.

**보안 모델**:
- 페어된 에이전트는 ngrok 터널 경유로 26개 명령 allowlist만 호출 가능
- `tabPolicy: 'own-only'`: 페어된 에이전트는 자기가 만든 탭만 read/write 가능. 너가 사용 중인 탭엔 손 못 댐
- 듀얼 리스너 아키텍처: 로컬 호출과 터널 호출이 분리된 게이트로 라우팅

**언제 쓰나**: "다른 에이전트가 내 브라우저를 쓰게 해줘", "원격 브라우저 접근"

### `/scrape` — 웹 스크레이핑 (신규)

**역할**: 웹 페이지 데이터 추출. 새 인텐트의 첫 호출은 `$B` 프리미티브로 페이지를 드라이브해서 ~30초에 JSON 반환. 매칭하는 인텐트의 두 번째 호출은 코드화된 브라우저 스킬을 ~200ms에 실행.

**3가지 경로**:
- **Match**: 인텐트가 기존 스킬의 `triggers:` 배열과 매칭 → `$B skill run <name>` 200ms 실행
- **Prototype**: 새 인텐트 → `$B goto`/`$B html` 등 프리미티브로 드라이브 → JSON 반환 → `/skillify` 제안
- **Refusal**: 변형 인텐트(form fill, click, submit)는 `/automate` 스킬로 라우팅 (현재 `/automate`는 P0 TODO)

**언제 쓰나**: "scrape", "이 페이지 데이터 가져와", "extract", "pull"

### `/skillify` — 스킬화 (신규)

**역할**: 가장 최근 성공한 `/scrape` 플로우를 영구 브라우저 스킬로 코드화한다. 미래의 같은 인텐트 호출은 코드화된 스크립트를 ~200ms에 실행하고 다시 페이지를 드라이브하지 않는다.

**11단계 플로우**:
1. Provenance 가드: 최근 ≤10턴 내 `/scrape` 결과 확인. cold면 거부
2. 이름 + tier + 트리거 제안 (`AskUserQuestion`)
3. 마지막 시도의 `$B` 호출만 추출 (실패한 selector나 채팅 fragment는 leak 안 됨)
4. `script.ts` + `script.test.ts` + 픽스처 합성
5. 임시 디렉토리에 스테이징
6. `$B skill test` 실행
7. 사용자 승인 게이트
8. 원자적 commit (test pass + approval 둘 다 있어야)

테스트 실패 또는 거부 시: `rm -rf` 임시 디렉토리. 반쪽짜리 스킬은 디스크에 절대 안 남는다.

### `/setup-browser-cookies` — 세션 매니저

**역할**: 실제 브라우저(Chrome, Arc, Brave, Edge)의 쿠키를 헤드리스 세션으로 가져와 인증된 페이지도 테스트할 수 있게 한다.

**기능**:
- 인터랙티브 피커 UI로 쿠키 도메인 선택
- 별도 로그인 없이 인증된 페이지 테스트 가능
- v0.18.3.0+: Windows 지원 (Chrome 80+ AES-256-GCM, DPAPI 키 unwrap, Chrome 127+ App-Bound Encryption fallback)

### `/setup-deploy` — 배포 설정기

**역할**: `/land-and-deploy`를 위한 일회성 설정. 플랫폼, 프로덕션 URL, 배포 명령을 감지한다.

**지원 플랫폼**: Fly.io, Render, Vercel, Netlify, Heroku, GitHub Actions, 커스텀

---

## 8. 보안 & 안전장치

### `/cso` — 최고 보안 책임자

**역할**: OWASP Top 10 + STRIDE 위협 모델링 수행.

**특징**:
- 인프라 우선 보안 감사: 시크릿 고고학, 의존성 공급망, CI/CD 파이프라인 보안
- LLM/AI 보안, 스킬 공급망 스캐닝
- 17가지 오탐 제외 규칙
- 8/10 이상 신뢰도 게이트
- 각 발견사항에 구체적 익스플로잇 시나리오 포함
- 독립적 발견 검증

### `/careful` — 안전 가드레일

**역할**: 파괴적 명령 실행 전 경고.

**감지 대상**:
- `rm -rf`
- `DROP TABLE`
- `git push --force`
- `git reset --hard`
- `kubectl delete`
- 기타 파괴적 명령

**사용 방법**: "be careful"이라고 말하면 활성화. 언제든 경고를 오버라이드 가능.

### `/freeze` — 편집 잠금

**역할**: 파일 편집을 특정 디렉토리로 제한하여 디버깅 중 범위 밖의 우발적 변경을 방지한다.

### `/guard` — 풀 세이프티

**역할**: `/careful` + `/freeze`를 동시에 활성화. 프로덕션 작업 시 최대 안전 모드.

### `/unfreeze` — 잠금 해제

**역할**: `/freeze` 경계를 제거한다.

---

## 9. 유틸리티

### `/learn` — 학습 관리

**역할**: 프로젝트별 학습 내용을 관리한다.

**기능**:
- 세션 간 학습 내용 리뷰
- 키워드 검색
- 오래된 학습 내용 정리 (prune)
- 학습 내용 내보내기 (export)

### `/make-pdf` — 마크다운 → PDF (신규)

**역할**: 어떤 마크다운 파일이든 발행 품질 PDF로 변환한다. 적절한 1in 마진, 지능적 페이지 분할, 페이지 번호, 표지, 러닝 헤더, curly quotes와 em dash, 클릭 가능 TOC, 대각선 DRAFT 워터마크.

**드래프트 아티팩트가 아니라 완성된 아티팩트.**

**언제 쓰나**: "make a PDF", "export to PDF", "이 마크다운을 PDF로", "문서 생성"

### `/gstack-upgrade` — 셀프 업데이터

**역할**: gstack을 최신 버전으로 업그레이드한다. 글로벌/벤더 설치를 감지하고 양쪽을 동기화하며 변경 내용을 보여준다.

**사용 예시**:
```
You:    /gstack-upgrade
Claude: ✅ v0.15.1.0 → v1.29.0.0 업그레이드 완료
        [변경 내용 요약: 17개 신규 스킬, gbrain 통합, /scrape + /skillify, ...]
```

---

## 10. 세션 관리 (Context)

> **v1.x.x 변경**: 이전 `/checkpoint`이 Claude Code의 native `/checkpoint` rewind alias와 충돌해서 두 스킬로 분리됐다.

### `/context-save` — 컨텍스트 저장

**역할**: 작업 중인 상태를 스냅샷으로 저장한다. git 상태, 의사결정 내역, 남은 작업을 캡처해서 어떤 미래 세션이든 한 박자도 놓치지 않고 이어 받을 수 있게 한다.

**기능**:
- git 상태, decisions made, remaining work 캡처
- 컨텍스트 압축이나 세션 종료 후에도 복구 가능
- `/context-restore`와 페어로 사용

**언제 쓰나**: "save progress", "save state", "context save", "내 작업 저장해"

**이전 이름**: `/checkpoint` (Claude Code의 native rewind alias와 shadowing되던 문제로 리네임)

### `/context-restore` — 컨텍스트 복구

**역할**: 이전에 `/context-save`로 저장한 작업 컨텍스트를 복원한다. 기본적으로 모든 브랜치에 걸쳐 가장 최근 저장 상태를 로드해서 멈춘 지점에서 다시 시작 — Conductor 워크스페이스 핸드오프 사이에서도.

**기능**:
- 가장 최근 저장된 상태 자동 로드
- 모든 브랜치 검색 (기본값)
- 특정 브랜치 / 특정 시점 복구도 가능

**언제 쓰나**: "resume", "restore context", "where was I", "내가 어디까지 했더라"

### `/health` — 코드 품질 대시보드

**역할**: 프로젝트의 기존 도구(타입 체커, 린터, 테스트 러너, 데드 코드 탐지기, 셸 린터)를 래핑하여 가중 복합 0~10 점수를 산출하고 시간에 따른 추세를 추적한다.

**기능**:
- tsc, biome, knip, shellcheck, 테스트 러너 등 기존 도구 활용
- 가중치 기반 복합 점수 0~10 산출
- 점수가 떨어지면 정확히 무엇이 변했고 어디를 고쳐야 하는지 알려줌
- 시간에 따른 추세 추적

**사용 예시**:
```
You:    /health
Claude: Code Health: 8.2/10 (↑0.3 from last check)
        ✅ Types: 10/10 — no errors
        ✅ Lint: 9/10 — 2 warnings
        ⚠️ Tests: 7/10 — 3 failing
        ✅ Dead code: 9/10 — 1 unused export
        Fix: test/billing.test.ts:42 — assertion expects old schema
```

---

## 11. 메모리 (gbrain — v1.x.x+ 신규)

gstack 메모리는 v1.x 시리즈에서 **gbrain**으로 통합됐다. CEO 플랜, 학습, 회고, 디자인 문서, 리포트 등이 검색 가능한 단일 인덱스에 들어간다. 기기 간에도 동기화된다.

### `/setup-gbrain` — gbrain 설정 (신규)

**역할**: 이 코딩 에이전트를 위한 gbrain을 설정한다. CLI 설치, 로컬 PGLite/Supabase brain 초기화, MCP 등록, 원격마다 신뢰 정책 캡처. 한 명령으로 zero에서 "gbrain이 돌고 이 에이전트가 호출 가능"까지.

**4가지 설치 경로** (v1.27.0.0+):
- **PGLite**: 로컬 임베디드 SQLite 기반 brain. 의존성 없음, 즉시 동작
- **Supabase**: 클라우드 Postgres 기반 brain. 팀/기기 간 공유
- **Switch**: 기존 brain 인스턴스 사이 전환
- **Remote MCP** (신규): 원격 MCP URL + bearer 토큰 paste만으로 등록. 로컬 DB 불필요. Tailscale 노드, ngrok 엔드포인트, LAN 서버, 팀원 서버에서 돌아가는 brain 사용

**언제 쓰나**: "setup gbrain", "connect gbrain", "start gbrain", "install gbrain"

### `/sync-gbrain` — gbrain 동기화 (신규)

**역할**: gbrain을 이 레포의 코드 상태와 동기화하고, CLAUDE.md의 에이전트 검색 가이드를 갱신한다. `gstack-gbrain-sync` 오케스트레이터를 상태 점검·native 코드 surface 등록·capability 검사·verdict 블록과 함께 래핑한다. 재실행 가능, 멱등.

**v1.29.0.0 신규: 워크트리 인식**:
- 각 Conductor 워크트리가 **자체 gbrain 소스**로 등록 (`gstack-code-<slug>-<pathhash8>`)
- `.gbrain-source` 핀 파일을 워크트리 루트에 작성
- 후속 `gbrain code-def`, `code-refs`, `code-callers` 호출이 자동으로 그 워크트리의 브랜치 상태로 라우팅
- 같은 origin의 N개 sibling 워크트리가 동시에 인덱싱 가능 (last-sync-wins 회피)

**언제 쓰나**: "sync gbrain", "refresh gbrain", "re-index this repo", "gbrain search isn't finding things"

**최소 gbrain 버전**: v0.30.0+ (`sources attach` 명령 필요)

---

[← README로 돌아가기](../README.md) · [트러블슈팅 →](troubleshooting.md)
