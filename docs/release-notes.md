# 릴리스 노트

> gstack 버전별 변경 이력 (v0.14.0.0 ~ v1.29.0.0)
>
> v0.15.1.0 ~ v1.29.0.0 사이 60+ 패치 빌드는 메이저 마일스톤만 정리했다. 전체 내역은 [공식 CHANGELOG](https://github.com/garrytan/gstack/blob/main/CHANGELOG.md) 참조.

[← README로 돌아가기](../README.md)

---

## v1.29.0.0 (2026-05-08) — 워크트리 인식 코드 검색

`/sync-gbrain`이 Conductor 워크트리마다 별도 gbrain 소스로 등록한다. 같은 레포의 병렬 브랜치 N개를 동시에 인덱싱해도 마지막 sync가 다른 워크트리의 인덱스를 덮어쓰지 않는다. `gbrain code-def`, `code-refs`, `code-callers` 호출이 그 워크트리의 브랜치 상태에서 결과를 반환한다.

**변경사항**:
- 워크트리 인식 소스 ID: `gstack-code-<slug>-<pathhash8>` 패턴으로 같은 origin의 워크트리들이 공존
- `.gbrain-source` 핀 파일을 워크트리 루트에 작성, 모든 하위 디렉토리에서 자동 라우팅
- gbrain CLI v0.30.0+ 필요 (`sources attach` 사용)

---

## v1.28.0.0 (2026-05-07) — Browse가 진짜 자동화를 한다

브라우저가 인증 SOCKS5 프록시, 컨테이너 Xvfb, 브라우저 native 다운로드를 처리한다. 거기에 에이전트가 한 번에 크롤하는 단일 파일 인덱스 `llms.txt`도 추가됐다.

**신규 기능**:
- **`browse --proxy`**: SOCKS5(인증 포함), HTTP, HTTPS 지원. 임베디드 SOCKS5 브릿지로 Chromium이 인증 업스트림 사용 가능
- **`browse --headed` Linux 자동 Xvfb**: DISPLAY 없는 컨테이너에서 첫 빈 디스플레이 자동 spawn
- **`download --navigate`**: 브라우저 native 다운로드 핸들러 사용 (Content-Disposition, 멀티홉 CDN 리다이렉트, 안티봇 CDN 체인)
- **`gstack/llms.txt` 자동 생성**: 47개 스킬 + 75개 browse 명령을 11KB 단일 인덱스로 노출. 에이전트가 한 번 fetch로 전체 surface 파악
- **stealth 축소**: `navigator.webdriver` 마스킹만 유지. plugins/languages 가짜 값은 오히려 봇 감지를 도와서 제거

---

## v1.27.0.0 (2026-05-06) — Remote MCP gbrain + 아티팩트 리네임

`/setup-gbrain`이 4번째 경로를 추가했다. 원격 MCP URL과 bearer 토큰을 붙여넣기만 하면 로컬 brain DB 설치 없이 gbrain MCP를 등록한다. 이미 다른 곳(Tailscale 노드, ngrok 엔드포인트, LAN, 팀 서버)에서 돌아가는 brain을 한 번의 paste로 사용.

**변경사항**:
- **Path 4 (Remote MCP)**: PGLite/Supabase 없이 원격 brain 등록. 30초 setup, 에이전트 가이드
- **`gstack-brain-$USER` → `gstack-artifacts-$USER`** 리포 리네임. 저널링된 인터럽트 안전 마이그레이션
- **사설 아티팩트 리포 자동 프로비저닝**: GitHub(`gh`) 또는 GitLab(`glab`) 자동, 또는 수동 URL paste

---

## v1.20.0.0 (2026-04-28) — 브라우저 스킬: `/scrape` + `/skillify`

`/scrape <intent>` 첫 호출은 페이지를 드라이브해서 ~30초에 JSON 반환, 두 번째 호출은 코드화된 스크립트를 ~200ms에 실행. `/skillify`가 성공한 플로우를 영구 브라우저 스킬로 합성한다.

**신규 기능**:
- **`/scrape` 스킬**: 단일 진입점. 매칭(기존 스킬 트리거 매칭) → 200ms / 프로토타입(브라우저 드라이브) → JSON / 거부(변형 인텐트는 `/automate`로)
- **`/skillify` 스킬**: 11단계 플로우. 마지막 시도의 `$B` 호출만 추출 → `script.ts` + `script.test.ts` + 픽스처 합성 → 임시 디렉토리에 스테이징 → 테스트 통과 + 사용자 승인 후 원자적 commit
- **3-tier 저장**: project > global > bundled (first-wins)
- **per-spawn 스코프 토큰**: 각 스킬 spawn이 자체 capability 토큰 받음, exit 시 revoke
- **번들 레퍼런스 스킬**: `hackernews-frontpage` (HN 프론트 페이지 → 30개 스토리 JSON)

---

## v1.17.0.0 (2026-04-26) — gstack 메모리가 진짜 gbrain에 산다

지난 한 달간 `/setup-gbrain`을 돌리고 `gbrain search`가 CEO 플랜·학습·회고를 못 찾았다면, Step 7이 `consumers.json`에 `status: "pending"` 플레이스홀더를 쓰고 끝낸 게 원인이었다. 이 릴리스는 그 접근을 폐기하고 gbrain v0.18.0의 federation surface(`gbrain sources` + `gbrain sync`)를 사용한다.

**변경사항**:
- 업그레이드 후 `/setup-gbrain`이 brain 리포의 `git worktree`를 추가하고 federated source로 등록, 초기 sync 실행
- 후속 gstack 스킬의 end-of-run 사이클이 `gbrain sync`를 자동 실행
- `/gstack-upgrade`가 기존 사용자에게 일회성 마이그레이션 실행

---

## v1.10.0.0 (2026-04-23) — 플랜 리뷰 인터랙티브 다이얼로그 복원

v1.6.4.0 이후 Opus 4.7에서 플랜 리뷰가 한 번에 모든 발견사항을 리포트로 출력하던 회귀를 수정. 모든 `AskUserQuestion`이 D-numbered 결정 브리핑(ELI10, Stakes, Pros/Cons ✅/❌, Net) 형식으로 렌더링된다.

**신규 기능**:
- **Pros / Cons 결정 브리핑**: `D<N>` 헤더, ELI10, "Stakes if we pick wrong:", Recommendation, 옵션별 ✅/❌ 불릿(최소 2 pros + 1 con), 마지막 `Net:` 한 줄
- **Hard-stop 이스케이프**: 파괴적 일방통행 선택의 경우 `✅ No cons — this is a hard-stop choice`
- **중립 자세**: SELECTIVE EXPANSION 체리픽과 테이스트 콜은 `(recommended)` 라벨로 AUTO_DECIDE 동작 유지

**수정사항**:
- 플랜 리뷰 cadence 회귀: `generateModelOverlay`가 `generateAskUserFormat`보다 먼저 렌더링되어 "Batch your questions" 디렉티브가 우선 적용되던 문제 해결
- 16개 사이트의 이스케이프 해치 강화: "이슈 없으면 self-dismiss"를 Opus 4.7이 모든 finding에 적용하던 문제 차단

---

## v1.0.0.0 (2026-04-18) — V1 마일스톤: 빌더를 위한 글쓰기

모든 스킬 출력(tier 2+)이 기술 용어 첫 사용 시 한 문장 설명을 추가하고, 결과 중심 질문 형식("사용자에게 무엇이 깨지나" vs "이 엔드포인트가 멱등성인가")을 사용하며, 짧고 직접적인 문장을 유지한다. 기술자도 비기술자도 둘 다 이득.

**신규 기능**:
- **공통 jargon 리스트**: ~50개 기술 용어(idempotent, race condition, N+1, backpressure 등) 사전을 `scripts/jargon-list.json`에 보관
- **Terse opt-out**: `gstack-config set explain_level terse`로 이전 짧은 산문 스타일 복원
- **README hero 재작성**: "10K-20K LOC/day" 클레임 제거. 출하한 제품 + 기능 + AI 시대 코딩 속도에 집중
- **`/retro` 메트릭 변경**: 기능, 커밋, 머지된 PR을 먼저 보여주고 raw LOC는 컨텍스트로 강등

---

## v0.19.0.0 (2026-04-17) — `/plan-tune`: gstack이 너의 취향을 학습한다

같은 `AskUserQuestion`에 매번 같은 답을 한다면, 이 스킬이 gstack에게 그만 묻도록 가르친다. "changelog polish 그만 물어봐"라고 하면 gstack이 적어두고 그때부터 존중한다. 단 일방통행 도어(파괴적 작업, 아키텍처 분기, 보안 선택)는 안전이 선호도를 이긴다는 원칙으로 항상 묻는다.

**신규 기능**:
- **`/plan-tune` 스킬**: 5차원 빌더 프로필(scope appetite, risk tolerance, detail preference, autonomy, architecture care). 자기 진단 vs 행동 기반 추적 양쪽 표시
- **8개 빌더 archetypes**: Cathedral Builder, Ship-It Pragmatist, Deep Craft, Taste Maker, Solo Operator, Consultant, Wedge Hunter, Builder-Coach (Polymath fallback 포함)
- **인라인 `tune:` 피드백**: 스킬 질문에 `tune: never-ask` / `tune: always-ask` / 자유 영어로 답하면 gstack이 정규화해서 선호도로 저장
- **프로필 포이즈닝 방어**: 인라인 `tune:` 쓰기는 사용자 본인 채팅에서 온 prefix만 수용. 도구 출력, 파일 콘텐츠, PR 설명에서 온 건 거부 (Codex 리뷰에서 발견)

---

## v0.15.1.0 (2026-04-01) — Design Without Shotgun

`/design-html`을 `/design-shotgun` 없이도 바로 실행할 수 있다. CEO 플랜, 디자인 리뷰 아티팩트, 승인된 목업 등 어떤 디자인 컨텍스트가 있는지 감지하고 어떻게 진행할지 물어본다.

**변경사항**:
- `/design-html`이 세 가지 라우팅 모드로 동작: (A) `/design-shotgun`에서 승인된 목업, (B) CEO 플랜이나 디자인 변형이 있지만 공식 승인 없음, (C) 설명이나 PNG만으로 처음부터 디자인
- "승인된 디자인이 없습니다"로 차단하는 대신, 선택지를 제공하여 바로 진행 가능

---

## v0.15.0.0 (2026-04-01) — Session Intelligence

AI 세션이 이전에 무슨 일이 있었는지 기억한다. 플랜, 리뷰, 체크포인트, 헬스 점수가 컨텍스트 압축이나 세션 종료 후에도 살아남고, 세션 간에 누적된다.

**신규 기능**:
- **세션 타임라인**: 모든 스킬이 시작/완료 이벤트를 `timeline.jsonl`에 자동 기록. 로컬 전용, 텔레메트리 설정과 무관하게 항상 동작
- **컨텍스트 복구**: 압축이나 세션 시작 후, 최근 CEO 플랜/체크포인트/리뷰를 불러와 에이전트가 이전 결정과 진행 상황을 파악
- **크로스 세션 주입**: 세션 시작 시 현재 브랜치의 마지막 스킬 실행과 최신 체크포인트를 표시
- **예측 스킬 제안**: 최근 3개 세션이 패턴을 보이면 (review, ship, review) 다음에 필요한 스킬을 제안
- **웰컴 백 메시지**: 브랜치명, 마지막 스킬, 체크포인트 상태, 헬스 점수를 한 문단으로 브리핑
- **`/checkpoint` 스킬**: 작업 상태 스냅샷 저장/복구. git 상태, 의사결정 내역, 남은 작업을 캡처. 브랜치 간 리스팅으로 Conductor 워크스페이스 핸드오프 지원
- **`/health` 스킬**: 코드 품질 대시보드. tsc, biome, knip, shellcheck, 테스트를 래핑하여 복합 0~10 점수 산출. 점수가 떨어지면 정확히 무엇이 변했고 어디를 고쳐야 하는지 알려줌

---

## v0.14.6.0 (2026-03-31) — Recursive Self-Improvement

gstack이 자기 실수에서 배운다. CLI 오류, 잘못된 접근법, 프로젝트 특이사항을 자동 기록하고 다음 세션에서 활용한다.

**신규 기능**:
- **자동 학습 시스템**: 명령 실패나 프로젝트 특이사항을 자동 기록. "bun test에 --timeout 30000 필요" 같은 것을 기억해서 매번 10분씩 낭비하는 걸 방지
- **학습 요약 표시**: 프로젝트에 학습이 5개 이상이면 세션 시작 시 상위 3개를 표시
- **13개 스킬 학습 지원**: office-hours, plan-ceo-review, plan-eng-review, plan-design-review, design-review, design-consultation, cso, qa, qa-only, retro가 새로 학습에 참여. 이전에는 review, ship, investigate만 연결되어 있었음

**변경사항**:
- 이전의 contributor mode(수동 옵트인, 마크다운 리포트)를 자동 학습으로 대체. 18일간 한 번도 발동되지 않았던 기능을 설정 없이 동일한 인사이트를 캡처하는 방식으로 교체

---

## v0.14.5.0 (2026-03-31) — Ship Idempotency + Skill Prefix Fix

`/ship`을 재실행해도 VERSION이 두 번 범프되거나 CHANGELOG가 중복되지 않는다.

**수정사항**:
- **`/ship` 멱등성 보장**: 푸시 성공 후 PR 생성 실패 시(API 장애, 레이트 리밋) 재실행해도 이미 범프된 VERSION을 감지하고, 이미 올린 코드를 스킵하고, 기존 PR 바디를 업데이트
- **스킬 프리픽스 패치**: `./setup --prefix`와 `gstack-relink`가 SKILL.md의 `name:` 필드를 프리픽스에 맞게 패치. 이전에는 심링크만 프리픽스되고 실제 name 필드는 무시됨
- **PR 멱등성 검사**: 기존 PR이 `OPEN` 상태인지 확인하여 닫힌 PR이 새 PR 생성을 막지 않음

---

## v0.14.4.0 (2026-03-31) — Review Army: Parallel Specialist Reviewers

`/review`가 7종의 전문 리뷰어를 병렬로 투입한다. 한 에이전트가 거대한 체크리스트를 적용하는 대신, 각 전문가가 독립적으로 diff를 읽고 구조화된 JSON으로 결과를 출력한다.

**신규 기능**:
- **7종 전문 리뷰어**: 테스트 갭 + 유지보수성(항상 활성화), 보안/성능/데이터 마이그레이션/API 계약/Red Team(조건부 활성화)
- **핑거프린트 기반 중복 제거**: 2명 이상이 같은 file:line:category를 지적하면 "MULTI-SPECIALIST CONFIRMED"로 신뢰도 상승
- **PR 품질 점수**: 0~10 점수 산출. `/retro`에서 추세 확인 가능
- **학습 기반 리뷰어 프롬프트**: 각 전문가가 해당 도메인의 과거 학습을 프롬프트에 주입받아 시간이 갈수록 리뷰가 향상

---

## v0.14.3.0 (2026-03-31) — Always-On Adversarial Review + Scope Drift

모든 코드 리뷰에서 적대적 분석이 자동 실행된다. 5줄짜리 auth 변경도 500줄짜리 기능과 동일한 크로스 모델 분석을 받는다.

**신규 기능**:
- **상시 적대적 리뷰**: 모든 `/review`와 `/ship`에서 Claude 적대적 서브에이전트 + Codex 적대적 챌린지가 자동 실행. diff 크기 기반 스킵 로직 제거
- **스코프 드리프트 감지**: `/ship` 실행 시 "계획한 것만 만들었는지" 자동 검증. 스코프 크리프와 누락된 요구사항 감지. 결과는 PR 바디에 포함
- **플랜 모드 안전 작업**: 브라우저 스크린샷, 디자인 목업, Codex 외부 의견, `~/.gstack/` 쓰기가 플랜 모드에서 허용

**변경사항**:
- `codex_reviews=disabled`는 Codex 패스만 비활성화. Claude 적대적 서브에이전트는 항상 실행

---

## v0.14.2.0 (2026-03-30) — Sidebar CSS Inspector + Per-Tab Agents

Side Panel이 시각적 디자인 도구가 되었다. 페이지의 아무 요소나 클릭하면 CSS 규칙 캐스케이드, 박스 모델, 계산된 스타일을 확인할 수 있다.

**신규 기능**:
- **CSS Inspector**: 요소 클릭으로 CSS 규칙 캐스케이드, specificity 배지, 소스 file:line, 박스 모델 시각화를 Side Panel에서 확인
- **라이브 스타일 편집**: `$B style .selector property value`로 실시간 CSS 수정. `$B style --undo`로 되돌리기
- **탭별 에이전트**: 브라우저 탭마다 독립 에이전트 프로세스. 여러 페이지를 동시에 작업할 때 간섭 없음
- **LLM 기반 페이지 클린업**: 에이전트가 페이지를 의미론적으로 분석하고 불필요한 요소를 지능적으로 제거
- **Pretty 스크린샷**: `$B prettyscreenshot --cleanup --scroll-to ".pricing" ~/Desktop/hero.png`
- **중지 버튼**: 에이전트 작업 중 빨간 중지 버튼으로 현재 태스크 취소

**수정사항**:
- Inspector 메시지 allowlist 누락 수정 (Codex 리뷰에서 발견)
- 클린업 시 사이트 상단 고정 네비게이션 보존
- 에이전트 무한 스크린샷-하이라이트 루프 방지

---

## v0.14.1.0 (2026-03-30) — Comparison Board is the Chooser

디자인 비교 보드가 변형 리뷰 시 항상 자동으로 열린다. 인라인 이미지 + "어떤 게 좋아요?" 방식 대신 보드에서 평가, 댓글, 리믹스/재생성을 수행한다.

**변경사항**:
- 비교 보드가 기본 선택기. 디자인 변형 생성 후 보드가 자동으로 열리고, `feedback.json`으로 구조화된 피드백 수집
- `/plan-design-review`, `/design-shotgun`, `/design-consultation` 모두 적용
- 보드 서버 실패 시 인라인 폴백으로 자동 전환

---

## v0.14.0.0 (2026-03-30) — Design to Code

승인된 디자인 목업에서 프로덕션 품질 HTML을 한 명령으로 생성할 수 있다.

**신규 기능**:
- **`/design-html` 스킬**: `/design-shotgun`에서 승인된 목업을 Pretext 기반의 자체 완결형 HTML로 변환. 텍스트가 실제로 리플로우되고, 높이가 콘텐츠에 맞게 조정되며, 레이아웃이 동적
- **Pretext 번들**: 30KB Pretext 소스를 `design-html/vendor/pretext.js`에 포함. 오프라인, 외부 의존성 없는 HTML 출력
- **디자인 파이프라인 체이닝**: `/design-shotgun` → `/design-html`, `/design-consultation` → `/design-html`로 자연스러운 연결

---

[← README로 돌아가기](../README.md) · [명령어 레퍼런스](commands.md) · [트러블슈팅](troubleshooting.md)
