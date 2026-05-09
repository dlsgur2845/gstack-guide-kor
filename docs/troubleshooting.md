# 트러블슈팅 & FAQ

> gstack 사용 중 발생할 수 있는 문제와 해결 방법

[← README로 돌아가기](../README.md)

---

## 자주 발생하는 문제

| 문제 | 해결 방법 |
|---|---|
| 스킬이 안 보임 | `cd ~/.claude/skills/gstack && ./setup` |
| `/browse` 실패 | `cd ~/.claude/skills/gstack && bun install && bun run build` |
| 오래된 설치 | `/gstack-upgrade` 실행 또는 `auto_upgrade: true` 설정 |
| Codex에서 SKILL.md 에러 | `cd ~/.codex/skills/gstack && git pull && ./setup --host codex` |
| Codex CLI hang (`Reading additional input from stdin...`) | v0.18.4.0+ 자동 수정. 그래도 막히면 Codex CLI 0.120.3+ 설치 |
| Windows에서 문제 | Git Bash 또는 WSL 사용. Bun + Node.js 모두 PATH에 필요 |
| Windows install build error | v0.18.0.1+ 자동 수정. 업그레이드 후 `./setup` 재실행 |
| Apple Silicon `zsh: killed ./browse/dist/browse` | v0.18.4.0+ 자동 수정 (ad-hoc codesigning) |
| Claude가 스킬을 못 찾음 | 프로젝트 `CLAUDE.md`에 gstack 섹션 추가 필요 |
| 업그레이드 후 스킬 동작 이상 | `cd ~/.claude/skills/gstack && ./setup` 재실행 |
| `/open-gstack-browser` 창이 ~15초 후 닫힘 | v0.18.1.0+ 자동 수정. 업그레이드 |
| `/checkpoint`이 사라짐 | v1.x.x에서 `/context-save` + `/context-restore`로 분리됨 |
| `gbrain search`가 CEO 플랜을 못 찾음 | v1.17.0.0+ 자동 수정. `/gstack-upgrade` 후 `/setup-gbrain` 재실행 |
| Conductor 워크트리에서 `gbrain code-def`가 잘못된 결과 | v1.29.0.0+ 자동 수정. 각 워크트리에서 `/sync-gbrain` 실행 |
| `/pair-agent`이 `newtab`/`goto`에서 403 | v1.16.0.0+ 자동 수정 (allowlist 17 → 26) |

---

## 프라이버시 & 텔레메트리

gstack은 프라이버시를 최우선으로 한다.

### 텔레메트리 기본 설정

- **기본값은 OFF** — 명시적으로 동의하지 않으면 아무것도 전송되지 않는다
- 첫 실행 시 텔레메트리 동의 여부를 묻는다

### 텔레메트리 모드

| 모드 | 설명 |
|---|---|
| **community** | 스킬 사용 데이터 + 안정적 기기 ID로 트렌드 추적 |
| **anonymous** | 사용 여부만 카운트, 고유 ID 없음, 세션 연결 불가 |
| **off** | 완전 비활성화 (기본값) |

### 수집 항목 (동의 시)

- 스킬 이름
- 실행 시간
- 성공/실패 여부
- gstack 버전
- OS 종류

### 절대 수집하지 않는 것

- 코드 내용
- 파일 경로
- 레포 이름
- 브랜치 이름
- 프롬프트 내용

### 설정 변경

```bash
# 텔레메트리 끄기
gstack-config set telemetry off

# 커뮤니티 모드로 변경
gstack-config set telemetry community

# 익명 모드로 변경
gstack-config set telemetry anonymous
```

### 로컬 분석

```bash
# 로컬 JSONL 파일 기반 대시보드 확인
gstack-analytics
```

---

## 유용한 팁

### CLAUDE.md 설정

프로젝트마다 `CLAUDE.md`에 gstack 섹션을 추가하면 Claude가 스킬을 자동으로 인식한다:

```markdown
## gstack
Use /browse from gstack for all web browsing. Never use mcp__claude-in-chrome__* tools.
Available skills: /office-hours, /plan-ceo-review, /plan-eng-review, /plan-design-review,
/plan-devex-review, /plan-tune, /design-consultation, /design-shotgun, /design-html,
/review, /ship, /land-and-deploy, /canary, /benchmark, /benchmark-models, /browse,
/open-gstack-browser, /pair-agent, /qa, /qa-only, /design-review, /devex-review,
/setup-browser-cookies, /setup-deploy, /retro, /investigate, /document-release,
/landing-report, /codex, /cso, /autoplan, /careful, /freeze, /guard, /unfreeze,
/learn, /gstack-upgrade, /context-save, /context-restore, /health, /scrape, /skillify,
/setup-gbrain, /sync-gbrain, /make-pdf.
```

### 자동 업그레이드

수동으로 `/gstack-upgrade`를 실행하는 대신, 자동 업그레이드를 설정할 수 있다:

```bash
gstack-config set auto_upgrade true
```

### Proactive 모드

gstack은 대화 맥락에서 필요한 스킬을 자동으로 제안할 수 있다. 예를 들어 "이거 동작하나?"라고 물으면 `/qa`를 제안하고, 버그를 만나면 `/investigate`를 제안한다.

```bash
# 끄기
gstack-config set proactive false

# 켜기 (기본값)
gstack-config set proactive true
```

### 학습 시스템

`/learn` 명령으로 프로젝트별 학습 내용을 세션 간에 유지할 수 있다. gstack이 작업하면서 배운 것들을 다음 세션에서도 활용한다.

---

## 지원 환경

| 환경 | 지원 |
|---|---|
| macOS (Intel / Apple Silicon) | 완전 지원 (v0.18.4.0+ M-시리즈 ad-hoc codesigning) |
| Linux | 완전 지원 (v1.28.0.0+ 컨테이너 자동 Xvfb) |
| Windows (Git Bash / WSL) | 지원 (Bun + Node.js 필요, v0.18.0.1+ 빌드 안정화) |
| Claude Code CLI | 완전 지원 |
| Claude Code Desktop (Mac/Windows) | 완전 지원 |
| Claude Code Web (claude.ai/code) | 완전 지원 |
| VS Code / JetBrains 확장 | 완전 지원 |
| OpenAI Codex CLI | SKILL.md 표준으로 지원 (v0.18.4.0+ 안정적 timeout 처리) |
| Gemini CLI | SKILL.md 표준으로 지원 |
| Cursor | SKILL.md 표준으로 지원 |
| OpenCode | `./setup --host opencode`로 한 명령 설치 (v0.18.3.0+) |

---

[← README로 돌아가기](../README.md) · [명령어 레퍼런스](commands.md)
