# 스프린트 워크플로우

> gstack의 핵심: 각 스킬이 스프린트 순서에 맞게 연결되는 프로세스

[← README로 돌아가기](../README.md)

---

## 스프린트 흐름

gstack은 단순한 도구 모음이 아닌 **프로세스**이다. 각 스킬이 스프린트 순서에 맞게 연결된다:

```
Think → Plan → Design → Build → Review → Test → Ship → Monitor → Reflect
```

`/office-hours`가 작성한 디자인 문서를 `/plan-ceo-review`가 읽고, `/plan-eng-review`가 작성한 테스트 플랜을 `/qa`가 활용하며, `/review`가 잡은 버그를 `/ship`이 수정 여부를 검증한다.

### 단계별 스킬 매핑

| 단계 | 스킬 | 하는 일 |
|---|---|---|
| **Think** | `/office-hours` | 아이디어를 6가지 질문으로 검증, 디자인 문서 생성 |
| **Plan** | `/plan-ceo-review` → `/plan-eng-review` → `/plan-design-review` → `/plan-devex-review` | 제품·기술·디자인·DX 관점에서 순차 리뷰 |
| | `/autoplan` | 위 4개 리뷰를 한 명령으로 자동 수행 |
| | `/plan-tune` | 질문 민감도와 빌더 archetype 자기 튜닝 |
| **Design** | `/design-consultation` | 경쟁 리서치 후 디자인 시스템 구축 |
| | `/design-shotgun` | 여러 AI 디자인 변형 생성 및 비교 |
| | `/design-html` | 플랜, 설명, PNG 등 어떤 출발점에서든 프로덕션 HTML/CSS 생성 |
| **Build** | 직접 구현 | 플랜 모드 종료 후 코드 작성 |
| **Review** | `/review` | 7종 병렬 전문 리뷰어 + 적대적 분석으로 버그 탐지 |
| | `/codex` | 독립적 세컨드 오피니언 (자동 실행됨) |
| | `/devex-review` | 라이브 DX 감사 (실제 docs 탐색, TTHW 측정) |
| **Test** | `/qa` | 실제 브라우저로 테스트 → 수정 → 재검증 |
| | `/benchmark` | 성능 회귀 감지 |
| | `/benchmark-models` | Claude·GPT·Gemini 비교 (지연·토큰·비용) |
| | `/cso` | 보안 감사 |
| **Ship** | `/ship` | 테스트 → 스코프 드리프트 감지 → PR 생성 (멱등성) |
| | `/landing-report` | 워크스페이스 인식 PR 큐 대시보드 |
| | `/land-and-deploy` | PR 머지 → 배포 → 헬스 체크 |
| **Monitor** | `/canary` | 배포 후 실시간 모니터링 |
| **Document** | `/document-release` | 출하 후 문서 자동 동기화 |
| | `/make-pdf` | 마크다운 → 발행 품질 PDF |
| **Reflect** | `/retro` | 주간 회고, 기여자별 분석 |
| **Session** | `/context-save` | 작업 상태 저장 (이전 `/checkpoint`에서 분리) |
| | `/context-restore` | 저장된 상태 복구 (이전 `/checkpoint resume`에서 분리) |
| | `/health` | 코드 품질 0~10 점수 대시보드, 추세 추적 |
| **Memory** | `/setup-gbrain` | 검색 가능한 메모리 설정 (4 경로) |
| | `/sync-gbrain` | 워크트리 인식 코드 인덱스 동기화 |
| **Browser** | `/browse` | Chromium 브라우저 (SOCKS5 프록시 + Linux Xvfb) |
| | `/open-gstack-browser` | 사이드바 확장 내장 가시 브라우저 |
| | `/pair-agent` | 다른 AI 에이전트에 브라우저 공유 |
| | `/scrape` | 웹 페이지 데이터 추출 |
| | `/skillify` | `/scrape` 플로우를 영구 스킬로 코드화 |

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

# 4. 디자인 탐색 (선택)
You:    /design-shotgun
Claude: [4가지 디자인 변형 생성 → 비교 보드 → 피드백 수집]

You:    /design-html
Claude: [선택한 디자인을 프로덕션 HTML/CSS로 변환]

# 5. 계획 확정 & 구현
You:    계획 승인. 플랜 모드 종료.
Claude: [11개 파일에 2,400줄 작성. ~8분.]

# 6. 코드 리뷰
You:    /review
Claude: [7 specialists dispatched in parallel]
        [AUTO-FIXED] 2개 이슈
        [ASK] 레이스 컨디션 → 승인 후 수정
        PR Quality Score: 9.0/10

# 7. QA 테스트
You:    /qa https://staging.myapp.com
Claude: [실제 브라우저로 테스트 → 버그 발견 → 수정 → 재검증]

# 8. 보안 감사 (선택)
You:    /cso
Claude: [OWASP Top 10 + STRIDE 위협 모델링 수행]

# 9. 배포
You:    /ship
Claude: Tests: 42 → 51 (+9 신규).
        Scope drift: clean (계획과 구현 일치)
        PR: github.com/you/app/pull/42

# 10. 프로덕션 랜딩
You:    /land-and-deploy
Claude: [PR 머지 → CI 대기 → 배포 → 헬스 체크]

# 11. 카나리 모니터링
You:    /canary
Claude: [콘솔 에러, 성능 회귀, 페이지 실패 감시]

# 12. 회고
You:    /retro
Claude: [이번 주 기여 분석, 테스트 건강도, 성장 기회]
```

---

## 병렬 스프린트

gstack은 하나의 스프린트에서도 효과적이지만, **10개를 동시에 실행할 때 진가를 발휘한다.**

[Conductor](https://conductor.build)를 사용하면 여러 Claude Code 세션을 병렬로 실행할 수 있다. 각 세션은 자체 격리된 워크스페이스에서 동작하며, 하나는 `/office-hours`, 다른 하나는 `/review`, 세 번째는 기능 구현, 네 번째는 `/qa` 실행... 모두 동시에.

스프린트 구조가 병렬 처리를 가능하게 한다. 프로세스 없이 10개의 에이전트는 10개의 혼돈일 뿐이다. 프로세스가 있으면 각 에이전트가 정확히 무엇을 해야 하고 언제 멈춰야 하는지 안다.

---

## 세션 인텔리전스 (v0.15.0.0+)

gstack은 세션 간 컨텍스트를 유지한다. 플랜, 리뷰, 체크포인트, 헬스 점수가 세션이 끝나도 살아남는다.

### 자동으로 일어나는 일

- **타임라인 기록**: 모든 스킬이 시작/완료 이벤트를 `timeline.jsonl`에 자동 기록. 로컬 전용, 절대 외부 전송 없음
- **컨텍스트 복구**: 세션 시작 시 최근 CEO 플랜, 체크포인트, 리뷰를 자동으로 불러와서 에이전트가 이전 결정과 진행 상황을 이어받음
- **크로스 세션 주입**: 세션 시작 시 현재 브랜치의 마지막 스킬 실행과 최신 체크포인트를 표시
- **예측 스킬 제안**: 최근 3개 세션이 패턴을 보이면 (예: review → ship → review) 다음에 필요한 스킬을 제안
- **웰컴 백 메시지**: 브랜치명, 마지막 스킬, 체크포인트 상태, 헬스 점수를 한 문단으로 브리핑

### v1.x.x: 컨텍스트 저장 분리

이전 `/checkpoint` 스킬이 Claude Code의 native `/checkpoint` rewind alias와 충돌했다. 두 스킬로 분리됐다:

- `/context-save`: 작업 상태 저장 (예전 `/checkpoint`)
- `/context-restore`: 저장된 상태 복구 (예전 `/checkpoint resume`)

기능은 동일. 이름만 충돌 회피로 변경.

### 자동 학습 (v0.14.6.0)

gstack은 자기 실수에서 배운다. CLI 오류, 잘못된 접근법, 프로젝트 특이사항을 자동 기록하고 다음 세션에서 활용한다.

- "bun test에 --timeout 30000 필요" 같은 프로젝트 특이사항을 기억
- 13개 스킬이 학습을 읽고 기여 (office-hours, plan-ceo-review, qa, review, ship 등)
- 프로젝트에 학습이 5개 이상이면 세션 시작 시 상위 3개를 자동 표시
- `/learn`으로 학습 내용 리뷰, 검색, 정리 가능

---

## `/autoplan`으로 빠르게 시작하기

매번 `/plan-ceo-review` → `/plan-design-review` → `/plan-eng-review` → `/plan-devex-review`를 순서대로 실행하는 게 번거롭다면, `/autoplan` 하나로 전부 자동 수행할 수 있다.

```
You:    /autoplan
Claude: [CEO 리뷰 → 디자인 리뷰 → 엔지니어링 리뷰 → DX 리뷰 순차 자동 수행]
        [의사결정 원칙에 따라 자동 판단]
        [사람의 테이스트가 필요한 결정만 질문]
```

6가지 의사결정 원칙을 인코딩해서 대부분의 리뷰 결정을 자동으로 처리하고, 정말 사람의 판단이 필요한 "테이스트 결정"만 사용자에게 물어본다.

v1.10.0.0 이후 모든 `AskUserQuestion`이 D-numbered 결정 브리핑(ELI10, Stakes, Pros/Cons ✅/❌, Net) 형식으로 렌더링된다. 5초 안에 옵션 고르거나, 생각이 필요하면 pros/cons 펼쳐 보면 된다.

---

## 검색 가능한 메모리: gbrain (v1.x.x+)

gstack의 메모리는 v1.x 시리즈에서 **gbrain**으로 통합됐다. CEO 플랜, 학습, 회고, 디자인 문서, 리포트가 검색 가능한 단일 인덱스에 들어가고 기기 간에도 동기화된다.

### 4가지 설치 경로 (`/setup-gbrain`)

```
You:    /setup-gbrain
Claude: 어떻게 설치할까요?
        A) PGLite — 로컬 임베디드, 의존성 없음
        B) Supabase — 클라우드 Postgres, 팀/기기 간 공유
        C) Switch — 기존 brain 사이 전환
        D) Remote MCP — 원격 brain 사용 (Tailscale, ngrok, LAN 서버)
```

Path D는 v1.27.0.0 신규. URL + bearer 토큰만 paste하면 로컬 DB 설치 없이 끝. 30초 setup.

### 워크트리 인식 코드 검색 (v1.29.0.0)

`/sync-gbrain`이 각 Conductor 워크트리를 자체 gbrain 소스로 등록한다. 같은 레포의 N개 sibling 워크트리를 동시에 인덱싱해도 각자의 브랜치 상태에서 결과 반환. `gbrain code-def`, `code-refs`, `code-callers`가 워크트리별 정확한 결과를 보여준다.

```bash
# 각 워크트리에서 한 번씩 실행
$ /sync-gbrain
✅ Indexed: gstack-code-myapp-3a7b2c1d (worktree: feature/billing)

$ gbrain code-def "processPayment"
# 이 워크트리의 브랜치 상태에서 정확한 결과 반환
```

`gbrain autopilot --install`을 머신당 한 번 실행하면 백그라운드 자동 sync로 전환된다.

---

## 브라우저 스킬: `/scrape` + `/skillify` (v1.20.0.0+)

웹 페이지에서 데이터 가져올 때 같은 페이지를 매번 30초씩 드라이브하는 대신, 첫 호출 후엔 200ms에 끝낸다.

```
# 첫 호출: 프로토타입
You:    /scrape latest hacker news stories
Claude: [`$B goto`/`$B html`로 페이지 드라이브 → JSON 반환] (~30초)
        다음 번에 200ms로 돌리고 싶으면 /skillify 실행하세요.

# 영구 스킬화
You:    /skillify
Claude: 이름: hackernews-frontpage
        Tier: project? global? bundled?
        [마지막 시도의 $B 호출만 추출 → 합성 → 임시 디렉토리 → 테스트 → 승인]
        ✅ 스킬 commit 완료

# 두 번째 호출: 코드화된 스크립트
You:    /scrape latest hacker news stories
Claude: [`$B skill run hackernews-frontpage` → JSON] (~200ms)
```

**3-tier 저장**: project > global > bundled (first-wins). 번들 스킬 `hackernews-frontpage`로 시작해보면 좋다.

**Trust 모델**: 각 스킬 spawn이 자체 scoped capability 토큰 받음. read+write만 (`eval`/`js`/`cookies`/`storage` 없음). exit 시 자동 revoke.

---

[← README로 돌아가기](../README.md) · [명령어 레퍼런스 →](commands.md)
