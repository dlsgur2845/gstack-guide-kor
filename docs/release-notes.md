# 릴리스 노트

> gstack 버전별 변경 이력 (v0.14.0.0 ~ v0.15.1.0)

[← README로 돌아가기](../README.md)

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
