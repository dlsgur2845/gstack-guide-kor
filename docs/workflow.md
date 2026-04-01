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
| **Plan** | `/plan-ceo-review` → `/plan-eng-review` → `/plan-design-review` | 제품·기술·디자인 관점에서 순차 리뷰 |
| | `/autoplan` | 위 3개 리뷰를 한 명령으로 자동 수행 |
| **Design** | `/design-consultation` | 경쟁 리서치 후 디자인 시스템 구축 |
| | `/design-shotgun` | 여러 AI 디자인 변형 생성 및 비교 |
| | `/design-html` | 승인된 목업을 프로덕션 HTML/CSS로 변환 |
| **Build** | 직접 구현 | 플랜 모드 종료 후 코드 작성 |
| **Review** | `/review` | 7종 병렬 전문 리뷰어 + 적대적 분석으로 버그 탐지 |
| | `/codex` | 독립적 세컨드 오피니언 (자동 실행됨) |
| **Test** | `/qa` | 실제 브라우저로 테스트 → 수정 → 재검증 |
| | `/benchmark` | 성능 회귀 감지 |
| | `/cso` | 보안 감사 |
| **Ship** | `/ship` | 테스트 → 스코프 드리프트 감지 → PR 생성 (멱등성) |
| | `/land-and-deploy` | PR 머지 → 배포 → 헬스 체크 |
| **Monitor** | `/canary` | 배포 후 실시간 모니터링 |
| **Reflect** | `/retro` | 주간 회고, 기여자별 분석 |

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

## `/autoplan`으로 빠르게 시작하기

매번 `/plan-ceo-review` → `/plan-design-review` → `/plan-eng-review`를 순서대로 실행하는 게 번거롭다면, `/autoplan` 하나로 전부 자동 수행할 수 있다.

```
You:    /autoplan
Claude: [CEO 리뷰 → 디자인 리뷰 → 엔지니어링 리뷰 순차 자동 수행]
        [의사결정 원칙에 따라 자동 판단]
        [사람의 테이스트가 필요한 결정만 질문]
```

6가지 의사결정 원칙을 인코딩해서 대부분의 리뷰 결정을 자동으로 처리하고, 정말 사람의 판단이 필요한 "테이스트 결정"만 사용자에게 물어본다.

---

[← README로 돌아가기](../README.md) · [명령어 레퍼런스 →](commands.md)
