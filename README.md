# gstack 완벽 가이드 (한국어)

> Claude Code를 가상 소프트웨어 개발팀으로 전환하는 오픈소스 슬래시 명령어 모음

## 개요

**gstack**은 Y Combinator CEO **Garry Tan**이 오픈소스로 공개한 Claude Code용 슬래시 명령어 팩이다. Claude Code를 단일 범용 어시스턴트가 아닌, CEO·엔지니어링 매니저·디자이너·QA 리드·보안 담당자 등 역할별 전문가 팀으로 운용할 수 있게 해준다.

핵심 철학은 **"기획은 리뷰가 아니고, 리뷰는 배포가 아니며, 파운더의 감각은 엔지니어링 엄밀함이 아니다"**라는 것으로, 개발 라이프사이클의 각 단계마다 서로 다른 인지 모드를 명시적으로 전환하여 각 단계가 탁월한 결과를 낼 수 있도록 한다.

- **GitHub**: [github.com/garrytan/gstack](https://github.com/garrytan/gstack)
- **공식 사이트**: [gstacks.org](https://gstacks.org)
- **라이선스**: MIT (무료, 영구 사용 가능)
- **버전**: v0.14.5.0 (2026년 3월 기준)
- **GitHub Stars**: ~45,700+

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
/design-consultation, /design-shotgun, /design-html, /review, /ship, /land-and-deploy,
/canary, /benchmark, /browse, /connect-chrome, /qa, /qa-only, /design-review,
/setup-browser-cookies, /setup-deploy, /retro, /investigate, /document-release, /codex,
/cso, /autoplan, /careful, /freeze, /guard, /unfreeze, /learn, /gstack-upgrade.
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

## 문서 구조

이 가이드는 가독성을 위해 여러 문서로 나뉘어 있다:

| 문서 | 내용 |
|---|---|
| **[스프린트 워크플로우](docs/workflow.md)** | gstack 스프린트 프로세스, 전체 워크플로우 예시, 병렬 스프린트 |
| **[명령어 레퍼런스](docs/commands.md)** | 31개 전체 명령어의 상세 설명과 사용 예시 |
| **[트러블슈팅 & FAQ](docs/troubleshooting.md)** | 문제 해결, 프라이버시, 텔레메트리, 팁 |

---

## 명령어 빠른 참조표

| 카테고리 | 명령어 | 역할 | 한줄 설명 |
|---|---|---|---|
| **기획** | `/office-hours` | YC 오피스 아워 | 6가지 강제 질문으로 아이디어의 전제를 도전하고 디자인 문서를 자동 생성 |
| | `/plan-ceo-review` | CEO / 파운더 | 디자인 문서를 읽고 10개 섹션으로 제품 리뷰, 4가지 스코프 모드 |
| | `/plan-eng-review` | 엔지니어링 매니저 | 아키텍처·데이터 플로우를 ASCII 다이어그램으로 시각화, 테스트 매트릭스 확정 |
| | `/plan-design-review` | 시니어 디자이너 | 각 디자인 차원을 0~10 평가, AI Slop 탐지, 인터랙티브 리뷰 |
| | `/design-consultation` | 디자인 파트너 | 경쟁 리서치 후 디자인 시스템 구축, DESIGN.md 자동 생성 |
| | `/autoplan` | 자동 리뷰 파이프라인 | CEO → 디자인 → 엔지니어링 리뷰를 한 명령으로 자동 수행 |
| **디자인** | `/design-shotgun` | 디자인 탐색 | 여러 AI 디자인 변형을 생성하고 비교 보드에서 피드백 수집 |
| | `/design-html` | 디자인 → 코드 | 승인된 AI 목업을 프로덕션 품질의 HTML/CSS로 변환 |
| **리뷰** | `/review` | 스태프 엔지니어 | 7종 병렬 전문 리뷰어로 프로덕션 버그 탐지, PR 품질 점수 산출 |
| | `/investigate` | 디버거 | 체계적 근본 원인 디버깅, 3회 실패 시 자동 중단 |
| | `/codex` | 세컨드 오피니언 | OpenAI Codex CLI를 통한 독립적 코드 리뷰, 3가지 모드 |
| **테스트** | `/qa` | QA 리드 | 실제 브라우저로 테스트 → 버그 수정 → 회귀 테스트 → 재검증 |
| | `/qa-only` | QA 리포터 | `/qa`와 동일하되 코드 수정 없이 버그 리포트만 생성 |
| | `/design-review` | 디자이너 + 코더 | 디자인 감사 후 UI/UX 문제를 직접 코드로 수정, 전/후 스크린샷 |
| | `/benchmark` | 퍼포먼스 엔지니어 | Core Web Vitals 측정, PR마다 전/후 성능 비교 |
| **배포** | `/ship` | 릴리스 엔지니어 | 테스트 → 스코프 드리프트 감지 → 푸시 → PR 생성 (멱등성 보장) |
| | `/land-and-deploy` | 릴리스 엔지니어 | PR 머지 → CI 대기 → 배포 → 프로덕션 헬스 체크 |
| | `/canary` | SRE | 배포 후 콘솔 에러·성능 회귀·페이지 실패 실시간 감시 |
| | `/document-release` | 테크니컬 라이터 | 배포 후 프로젝트 문서를 코드 변경에 맞게 자동 업데이트 |
| **회고** | `/retro` | 엔지니어링 매니저 | 기여자별 분석, 배포 기록 추적, 글로벌 회고 지원 |
| **브라우저** | `/browse` | QA 엔지니어 | 영속적 Chromium 세션, 명령당 ~100ms, 접근성 트리 참조 |
| | `/connect-chrome` | Chrome 연결 | CSS Inspector, 탭별 에이전트, 라이브 스타일 편집 지원 |
| | `/setup-browser-cookies` | 세션 매니저 | 실제 브라우저 쿠키를 헤드리스 세션으로 임포트 |
| | `/setup-deploy` | 배포 설정기 | `/land-and-deploy`를 위한 일회성 플랫폼·URL·배포 명령 설정 |
| **보안** | `/cso` | 최고 보안 책임자 | OWASP Top 10 + STRIDE 위협 모델링, 17가지 오탐 제외 규칙 |
| | `/careful` | 안전 가드레일 | `rm -rf`, `DROP TABLE` 등 파괴적 명령 실행 전 경고 |
| | `/freeze` | 편집 잠금 | 파일 편집을 특정 디렉토리로 제한 |
| | `/guard` | 풀 세이프티 | `/careful` + `/freeze` 동시 활성화, 최대 안전 모드 |
| | `/unfreeze` | 잠금 해제 | `/freeze` 편집 제한 해제 |
| **유틸** | `/learn` | 학습 관리 | 프로젝트별 학습 내용 리뷰·검색·정리·내보내기 |
| | `/gstack-upgrade` | 셀프 업데이터 | 최신 버전으로 업그레이드, 글로벌/벤더 설치 자동 동기화 |

---

*gstack v0.14.5.0 기준 · 마지막 업데이트: 2026년 3월 31일*
