# gstack 완벽 가이드

> Claude Code를 가상 소프트웨어 개발팀으로 전환하는 오픈소스 슬래시 명령어 모음

## 개요

**gstack**은 Y Combinator CEO **Garry Tan**이 오픈소스로 공개한 Claude Code용 슬래시 명령어 팩이다. Claude Code를 단일 범용 어시스턴트가 아닌, CEO·엔지니어링 매니저·디자이너·QA 리드·보안 담당자 등 역할별 전문가 팀으로 운용할 수 있게 해준다.

핵심 철학은 **"기획은 리뷰가 아니고, 리뷰는 배포가 아니며, 파운더의 감각은 엔지니어링 엄밀함이 아니다"**라는 것으로, 개발 라이프사이클의 각 단계마다 서로 다른 인지 모드를 명시적으로 전환하여 각 단계가 탁월한 결과를 낼 수 있도록 한다.

- **GitHub**: [github.com/garrytan/gstack](https://github.com/garrytan/gstack)
- **공식 사이트**: [gstacks.org](https://gstacks.org)
- **라이선스**: MIT (무료, 영구 사용 가능)
- **GitHub Stars**: ~45,700+ (2026년 3월 기준)
- **기술 스택**: TypeScript 73.9%, Go Template 21.9%, Shell 4.0%

---

## 설치

### 사전 요구사항

| 요구사항 | 설명 |
|---|---|
| Claude Code | Anthropic의 CLI 기반 에이전트 코딩 도구 |
| Git | 저장소 클론용 |
| Bun v1.0+ | `/browse` 바이너리 컴파일 및 의존성 설치용 |
| Node.js | Windows 환경에서만 필요 |

### Step 1: 개인 머신에 설치 (30초)

Claude Code를 열고 아래 명령을 붙여넣으면 된다:

```bash
git clone https://github.com/garrytan/gstack.git ~/.claude/skills/gstack \
  && cd ~/.claude/skills/gstack \
  && ./setup
```

설치 후 프로젝트의 `CLAUDE.md`에 gstack 섹션을 추가한다:

```markdown
## gstack
Use /browse from gstack for all web browsing. Never use mcp__claude-in-chrome__* tools.
Available skills: /office-hours, /plan-ceo-review, /plan-eng-review, /plan-design-review,
/design-consultation, /review, /ship, /land-and-deploy, /canary, /benchmark, /browse,
/qa, /qa-only, /design-review, /setup-browser-cookies, /setup-deploy, /retro,
/investigate, /document-release, /codex, /cso, /autoplan, /careful, /freeze, /guard,
/unfreeze, /gstack-upgrade.
```

### Step 2: 팀 프로젝트에 추가 (선택)

팀원들이 `git clone`만으로 gstack을 사용할 수 있도록 프로젝트 레포에 직접 커밋한다:

```bash
cp -Rf ~/.claude/skills/gstack .claude/skills/gstack \
  && rm -rf .claude/skills/gstack/.git \
  && cd .claude/skills/gstack \
  && ./setup
```

### Codex / Gemini CLI / Cursor 지원

gstack은 SKILL.md 표준을 지원하는 모든 에이전트에서 동작한다:

```bash
# 레포 단위 설치
git clone https://github.com/garrytan/gstack.git .agents/skills/gstack
cd .agents/skills/gstack && ./setup --host codex

# 사용자 전역 설치
git clone https://github.com/garrytan/gstack.git ~/gstack
cd ~/gstack && ./setup --host codex

# 자동 감지
cd ~/gstack && ./setup --host auto
```

---

## 스프린트 워크플로우

gstack은 단순한 도구 모음이 아닌 **프로세스**이다. 각 스킬이 스프린트 순서에 맞게 연결된다:

```
Think → Plan → Build → Review → Test → Ship → Reflect
```

`/office-hours`가 작성한 디자인 문서를 `/plan-ceo-review`가 읽고, `/plan-eng-review`가 작성한 테스트 플랜을 `/qa`가 활용하며, `/review`가 잡은 버그를 `/ship`이 수정 여부를 검증한다.

---

## 전체 명령어 레퍼런스

### 1. 기획 (Think & Plan)

#### `/office-hours` — YC 오피스 아워

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

#### `/plan-ceo-review` — CEO / 파운더

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

#### `/plan-eng-review` — 엔지니어링 매니저

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

#### `/plan-design-review` — 시니어 디자이너

**역할**: 각 디자인 차원을 0~10으로 평가하고, 10점이 어떤 모습인지 설명한 뒤, 계획을 편집하여 10점에 도달하게 한다.

**기능**:
- AI Slop 탐지 (AI가 만들어낸 저품질 디자인 감지)
- 인터랙티브: 디자인 선택마다 사용자에게 질문

#### `/design-consultation` — 디자인 파트너

**역할**: 처음부터 완전한 디자인 시스템을 구축한다.

**기능**:
- 경쟁 제품 리서치
- 안전한 선택과 창의적 리스크를 동시 제안
- 실제 제품의 현실적 목업 생성
- `DESIGN.md` 작성 → `/design-review`와 `/plan-eng-review`에 자동 전달

#### `/autoplan` — 자동 리뷰 파이프라인

**역할**: 한 명령으로 CEO → 디자인 → 엔지니어링 리뷰를 자동 수행한다.

**기능**:
- 의사결정 원칙을 인코딩하여 자동 리뷰
- 테이스트 결정(사람의 판단이 필요한 부분)만 사용자에게 질문

---

### 2. 리뷰 (Review)

#### `/review` — 스태프 엔지니어

**역할**: CI를 통과하지만 프로덕션에서 터지는 버그를 찾아낸다.

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

Claude: [AUTO-FIXED] 2개 이슈: import 정렬, 미사용 변수 제거
        [ASK] services/auth.ts:47 — 레이스 컨디션 발견.
        동시 로그인 시 세션 데이터가 덮어씌워질 수 있습니다.
        수정하시겠습니까? (Y/N)
```

#### `/investigate` — 디버거

**역할**: 체계적 근본 원인 디버깅. "조사 없는 수정은 없다"는 철칙을 따른다.

**규칙**:
- 데이터 플로우 추적
- 가설 테스트
- 3회 수정 실패 시 중단
- 조사 대상 모듈에 자동 freeze 적용

#### `/codex` — 세컨드 오피니언

**역할**: OpenAI Codex CLI를 통한 독립적 코드 리뷰.

**3가지 모드**:
- **review**: 통과/불합격 게이트
- **adversarial challenge**: 적대적 도전
- **open consultation**: 자유 상담
- `/review`와 `/codex` 모두 실행 시 크로스 모델 분석 가능

---

### 3. 테스트 & QA

#### `/qa` — QA 리드

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

#### `/qa-only` — QA 리포터

**역할**: `/qa`와 동일한 방법론이지만 **코드 수정 없이 버그 리포트만** 생성한다.

#### `/design-review` — 디자이너 + 코더

**역할**: `/plan-design-review`와 동일한 감사를 수행한 뒤, 발견한 문제를 직접 수정한다.

**기능**:
- 원자적 커밋으로 수정
- 수정 전/후 스크린샷 생성

#### `/benchmark` — 퍼포먼스 엔지니어

**역할**: 페이지 로드 타임, Core Web Vitals, 리소스 사이즈를 측정하고, PR마다 전/후 비교를 수행한다.

---

### 4. 배포 & 릴리스 (Ship)

#### `/ship` — 릴리스 엔지니어

**역할**: 메인 브랜치 동기화, 테스트 실행, 커버리지 감사, 푸시, PR 생성을 한 명령으로 처리한다.

**기능**:
- 테스트 프레임워크가 없으면 자동 부트스트랩
- 커버리지 확인

**사용 예시**:
```
You:    /ship

Claude: ✅ main 브랜치 동기화 완료
        ✅ 테스트 실행: 42 → 51 (+9 신규)
        ✅ 커버리지: 82% → 85%
        ✅ PR 생성: github.com/you/app/pull/42
```

#### `/land-and-deploy` — 릴리스 엔지니어

**역할**: PR 머지 → CI 대기 → 배포 → 프로덕션 헬스 체크를 한 명령으로 처리한다.

"승인됨"에서 "프로덕션 검증 완료"까지 한 번에.

#### `/canary` — SRE

**역할**: 배포 후 모니터링 루프. 콘솔 에러, 성능 회귀, 페이지 실패를 감시한다.

#### `/document-release` — 테크니컬 라이터

**역할**: 방금 배포한 내용에 맞게 프로젝트의 모든 문서를 업데이트한다. 오래된 README를 자동 감지한다.

---

### 5. 회고 (Reflect)

#### `/retro` — 엔지니어링 매니저

**역할**: 팀 인식 주간 회고.

**기능**:
- 기여자별 분석 (라인 수, 커밋 수, 테스트 건강도)
- 배포 연속기록 추적
- 성장 기회 제안
- `/retro global`: 모든 프로젝트와 AI 도구(Claude Code, Codex, Gemini)를 아우르는 글로벌 회고

---

### 6. 브라우저 자동화

#### `/browse` — QA 엔지니어

**역할**: 실제 Chromium 브라우저로 클릭, 네비게이션, 스크린샷을 수행한다.

**특징**:
- 초기 시작 후 명령당 ~100ms 응답
- 쿠키, 탭, localStorage가 명령 간 유지
- 30분 유휴 시 자동 종료
- CSS 셀렉터 대신 접근성 트리 참조(@e1, @e2) 사용
- localhost 전용 바인딩, Bearer 토큰 인증

**사용 예시**:
```
You:    /browse goto https://example.com
Claude: [페이지 로딩, 접근성 트리 반환]

You:    /browse snapshot
Claude: [현재 페이지 스크린샷 캡처]
```

#### `/setup-browser-cookies` — 세션 매니저

**역할**: 실제 브라우저(Chrome, Arc, Brave, Edge)의 쿠키를 헤드리스 세션으로 가져와 인증된 페이지도 테스트할 수 있게 한다.

#### `/setup-deploy` — 배포 설정기

**역할**: `/land-and-deploy`를 위한 일회성 설정. 플랫폼, 프로덕션 URL, 배포 명령을 감지한다.

---

### 7. 보안 & 안전장치

#### `/cso` — 최고 보안 책임자

**역할**: OWASP Top 10 + STRIDE 위협 모델링 수행.

**특징**:
- 17가지 오탐 제외 규칙
- 8/10 이상 신뢰도 게이트
- 각 발견사항에 구체적 익스플로잇 시나리오 포함
- 독립적 발견 검증

#### `/careful` — 안전 가드레일

**역할**: 파괴적 명령 실행 전 경고.

**감지 대상**:
- `rm -rf`
- `DROP TABLE`
- `git push --force`
- `git reset --hard`

**사용 방법**: "be careful"이라고 말하면 활성화. 언제든 경고를 오버라이드 가능.

#### `/freeze` — 편집 잠금

**역할**: 파일 편집을 특정 디렉토리로 제한하여 디버깅 중 범위 밖의 우발적 변경을 방지한다.

#### `/guard` — 풀 세이프티

**역할**: `/careful` + `/freeze`를 동시에 활성화. 프로덕션 작업 시 최대 안전 모드.

#### `/unfreeze` — 잠금 해제

**역할**: `/freeze` 경계를 제거한다.

---

### 8. 유틸리티

#### `/gstack-upgrade` — 셀프 업데이터

**역할**: gstack을 최신 버전으로 업그레이드한다. 글로벌/벤더 설치를 감지하고 양쪽을 동기화하며 변경 내용을 보여준다.

---

## 전체 워크플로우 예시

아래는 하나의 기능을 기획부터 배포까지 gstack으로 처리하는 전체 흐름이다:

```
# 1. 아이디어 탐색
You:    일일 캘린더 브리핑 앱을 만들고 싶어
You:    /office-hours
Claude: [6가지 질문 → 디자인 문서 자동 생성]

# 2. 제품 리뷰
You:    /plan-ceo-review
Claude: [디자인 문서 읽기 → 10개 섹션 리뷰 → 스코프 결정]

# 3. 기술 설계
You:    /plan-eng-review
Claude: [아키텍처 다이어그램, 실패 모드, 테스트 플랜]

# 4. 계획 확정 & 구현
You:    계획 승인. 플랜 모드 종료.
Claude: [11개 파일에 2,400줄 작성. ~8분.]

# 5. 코드 리뷰
You:    /review
Claude: [AUTO-FIXED] 2개 이슈
        [ASK] 레이스 컨디션 → 승인 후 수정

# 6. QA 테스트
You:    /qa https://staging.myapp.com
Claude: [실제 브라우저로 테스트 → 버그 발견 → 수정 → 재검증]

# 7. 배포
You:    /ship
Claude: Tests: 42 → 51 (+9 신규). PR: github.com/you/app/pull/42

# 8. 프로덕션 랜딩
You:    /land-and-deploy
Claude: [PR 머지 → CI 대기 → 배포 → 헬스 체크]

# 9. 카나리 모니터링
You:    /canary
Claude: [콘솔 에러, 성능 회귀, 페이지 실패 감시]

# 10. 회고
You:    /retro
Claude: [이번 주 기여 분석, 테스트 건강도, 성장 기회]
```

---

## 병렬 스프린트

gstack은 하나의 스프린트에서도 효과적이지만, **10개를 동시에 실행할 때 진가를 발휘한다.**

[Conductor](https://conductor.build)를 사용하면 여러 Claude Code 세션을 병렬로 실행할 수 있다. 각 세션은 자체 격리된 워크스페이스에서 동작하며, 하나는 `/office-hours`, 다른 하나는 `/review`, 세 번째는 기능 구현, 네 번째는 `/qa` 실행 — 모두 동시에.

스프린트 구조가 병렬 처리를 가능하게 한다. 프로세스 없이 10개의 에이전트는 10개의 혼돈일 뿐이다. 프로세스가 있으면 각 에이전트가 정확히 무엇을 해야 하고 언제 멈춰야 하는지 안다.

---

## 트러블슈팅

| 문제 | 해결 방법 |
|---|---|
| 스킬이 안 보임 | `cd ~/.claude/skills/gstack && ./setup` |
| `/browse` 실패 | `cd ~/.claude/skills/gstack && bun install && bun run build` |
| 오래된 설치 | `/gstack-upgrade` 실행 또는 `auto_upgrade: true` 설정 |
| Codex에서 SKILL.md 에러 | `cd ~/.codex/skills/gstack && git pull && ./setup --host codex` |
| Windows에서 문제 | Git Bash 또는 WSL 사용. Bun + Node.js 모두 PATH에 필요 |
| Claude가 스킬을 못 찾음 | 프로젝트 `CLAUDE.md`에 gstack 섹션 추가 필요 |

---

## 프라이버시 & 텔레메트리

- **기본값은 OFF** — 명시적으로 동의하지 않으면 아무것도 전송되지 않는다
- 수집 항목 (동의 시): 스킬 이름, 실행 시간, 성공/실패, gstack 버전, OS
- **절대 수집하지 않는 것**: 코드, 파일 경로, 레포 이름, 브랜치 이름, 프롬프트
- 변경 방법: `gstack-config set telemetry off`
- 로컬 분석: `gstack-analytics` 명령으로 로컬 JSONL 파일 기반 대시보드 확인

---

## 명령어 빠른 참조표

| 카테고리 | 명령어 | 역할 | 상세 설명 |
|---|---|---|---|
| **기획** | `/office-hours` | YC 오피스 아워 | 6가지 강제 질문으로 아이디어의 전제를 도전하고 프레이밍을 재구성함. 구현 대안과 노력 추정치를 제시하며, 디자인 문서를 자동 생성하여 이후 모든 스킬에 전달 |
| | `/plan-ceo-review` | CEO / 파운더 | 디자인 문서를 읽고 10개 섹션으로 제품 리뷰 수행. Expansion·Selective Expansion·Hold·Reduction 4가지 모드로 스코프를 도전하고 사용자 가치·시장 적합성을 평가 |
| | `/plan-eng-review` | 엔지니어링 매니저 | 아키텍처·데이터 플로우·상태 전이를 ASCII 다이어그램(시퀀스/상태/컴포넌트/데이터 플로우)으로 시각화하고, 엣지 케이스·실패 모드·테스트 매트릭스를 확정 |
| | `/plan-design-review` | 시니어 디자이너 | 각 디자인 차원을 0~10으로 평가하고 10점이 어떤 모습인지 설명. AI Slop(AI 생성 저품질 디자인) 탐지. 디자인 선택마다 인터랙티브하게 사용자에게 질문 |
| | `/design-consultation` | 디자인 파트너 | 경쟁 제품 리서치 후 안전한 선택과 창의적 리스크를 동시 제안. 실제 제품의 현실적 목업을 생성하고 DESIGN.md를 작성하여 `/design-review`와 `/plan-eng-review`에 전달 |
| | `/autoplan` | 자동 리뷰 파이프라인 | 한 명령으로 CEO → 디자인 → 엔지니어링 리뷰를 순차 자동 수행. 인코딩된 의사결정 원칙으로 판단하고, 사람의 테이스트가 필요한 결정만 사용자에게 질문 |
| **리뷰** | `/review` | 스태프 엔지니어 | diff를 분석하여 N+1 쿼리·레이스 컨디션·트러스트 바운더리 위반·누락된 인덱스·잘못된 재시도 로직 등 CI를 통과하지만 프로덕션에서 터지는 버그를 탐지. 명백한 문제는 AUTO-FIX, 판단 필요 시 ASK |
| | `/investigate` | 디버거 | "조사 없는 수정은 없다"는 철칙하에 데이터 플로우를 추적하고 가설을 테스트. 조사 대상 모듈에 자동 freeze 적용. 3회 수정 실패 시 자동 중단하여 무한 루프 방지 |
| | `/codex` | 세컨드 오피니언 | OpenAI Codex CLI를 통한 독립적 코드 리뷰. review(통과/불합격 게이트)·adversarial(적대적 도전)·open(자유 상담) 3가지 모드. `/review`와 함께 사용 시 크로스 모델 분석 |
| **테스트** | `/qa` | QA 리드 | git diff를 읽고 영향받는 페이지/라우트를 식별 → 실제 Chromium 브라우저로 테스트 → 버그 발견 시 원자적 커밋으로 수정 → 회귀 테스트 자동 생성 → 수정 후 재검증까지 전체 루프 수행 |
| | `/qa-only` | QA 리포터 | `/qa`와 동일한 방법론으로 실제 브라우저 테스트를 수행하되 코드 수정 없이 순수 버그 리포트만 생성. 수정 권한 없이 보고만 필요할 때 사용 |
| | `/design-review` | 디자이너 + 코더 | `/plan-design-review`와 동일한 디자인 감사를 수행한 뒤, 발견한 UI/UX 문제를 직접 코드로 수정. 원자적 커밋으로 반영하고 수정 전/후 스크린샷을 생성하여 비교 |
| | `/benchmark` | 퍼포먼스 엔지니어 | 페이지 로드 타임·Core Web Vitals(LCP, FID, CLS)·리소스 사이즈를 베이스라인으로 측정하고, PR마다 변경 전/후를 비교하여 성능 회귀를 사전에 감지 |
| **배포** | `/ship` | 릴리스 엔지니어 | main 브랜치 동기화 → 전체 테스트 실행 → 커버리지 감사 → 푸시 → PR 생성을 한 명령으로 처리. 테스트 프레임워크가 없으면 자동 부트스트랩하여 테스트 환경을 구축 |
| | `/land-and-deploy` | 릴리스 엔지니어 | "승인됨"에서 "프로덕션 검증 완료"까지 한 명령으로 처리. PR 머지 → CI 파이프라인 대기 → 실제 배포 실행 → 프로덕션 헬스 체크까지 전체 랜딩 프로세스 자동화 |
| | `/canary` | SRE | 배포 직후 모니터링 루프를 실행하여 콘솔 에러·성능 회귀·페이지 실패를 실시간 감시. 문제 발견 시 즉시 알림 |
| | `/document-release` | 테크니컬 라이터 | 방금 배포한 코드 변경사항과 프로젝트 문서(README, API 문서 등)를 비교하여 불일치를 자동 감지하고, 문서를 최신 상태로 업데이트 |
| **회고** | `/retro` | 엔지니어링 매니저 | 기여자별 라인 수·커밋 수·테스트 건강도를 분석하고, 배포 연속기록 추적 및 성장 기회를 제안. `/retro global`로 모든 프로젝트와 AI 도구(Claude Code, Codex, Gemini)를 아우르는 글로벌 회고 가능 |
| **브라우저** | `/browse` | QA 엔지니어 | 영속적 Chromium 세션으로 실제 클릭·네비게이션·스크린샷 수행. 초기 시작 후 명령당 ~100ms 응답. 쿠키·탭·localStorage가 명령 간 유지되며 30분 유휴 시 자동 종료. 접근성 트리 참조(@e1, @e2) 방식 |
| | `/setup-browser-cookies` | 세션 매니저 | 실제 브라우저(Chrome, Arc, Brave, Edge)의 쿠키를 헤드리스 세션으로 임포트하여, 로그인이 필요한 인증된 페이지도 별도 로그인 없이 테스트 가능하게 설정 |
| | `/setup-deploy` | 배포 설정기 | `/land-and-deploy`를 위한 일회성 초기 설정. 프로젝트의 플랫폼(Vercel, Railway, Fly.io 등)·프로덕션 URL·배포 명령을 자동 감지하여 배포 파이프라인을 구성 |
| **보안** | `/cso` | 최고 보안 책임자 | OWASP Top 10 + STRIDE 위협 모델링을 수행. 17가지 오탐 제외 규칙과 8/10 이상 신뢰도 게이트로 노이즈 제로. 각 발견사항에 구체적 익스플로잇 시나리오를 포함하고 독립적으로 검증 |
| | `/careful` | 안전 가드레일 | `rm -rf`·`DROP TABLE`·`git push --force`·`git reset --hard` 등 파괴적 명령 실행 전 경고를 표시. "be careful"로 활성화하며 개별 경고를 오버라이드 가능 |
| | `/freeze` | 편집 잠금 | 파일 편집을 지정한 하나의 디렉토리로 제한. 디버깅 중 Claude가 범위 밖의 관련 없는 코드를 실수로 변경하는 것을 방지 |
| | `/guard` | 풀 세이프티 | `/careful`(파괴적 명령 경고) + `/freeze`(편집 잠금)를 동시에 활성화하는 최대 안전 모드. 프로덕션 환경 작업 시 권장 |
| | `/unfreeze` | 잠금 해제 | `/freeze`로 설정한 편집 디렉토리 제한을 해제하여 전체 프로젝트에 대한 편집 권한을 복원 |
| **유틸** | `/gstack-upgrade` | 셀프 업데이터 | gstack을 최신 버전으로 업그레이드. 글로벌 설치와 프로젝트 벤더 설치를 자동 감지하여 양쪽을 동기화하고, 변경된 내용을 요약하여 표시 |

---

*마지막 업데이트: 2026년 3월 28일*
