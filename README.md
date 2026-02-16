# AI Config — Multi-AI Orchestration System

멀티 프로젝트 AI 오케스트레이션 시스템의 중앙 설정 저장소.

## 구조

```
C:\dev\02_ai_config\
├── docs/                   ← 아키텍처, 워크플로우, 결정 기록
├── gpt/                    ← GPT Plus 프롬프트
├── gemini/                 ← Gemini Pro 프롬프트
├── perplexity/             ← Perplexity Pro 프롬프트
├── claude/                 ← Claude Code 계층 설명
├── handoff_templates/      ← AI 간 핸드오프 템플릿
├── projects/               ← Junction Points (읽기 전용)
│   ├── orchestration → C:/dev/01_projects/01_orchestration/context
│   └── portfolio → C:/dev/01_projects/02_portfolio/context
└── .claude/
    ├── agents/             ← Orchestrator (멀티 프로젝트 검증)
    ├── skills/             ← /sync-all (전체 동기화)
    └── settings.json       ← Opus, permissions, hooks
```

## Quick Start

### 1. 세션 시작

```bash
cd C:\dev\02_ai_config
claude
```

→ SessionStart 훅이 자동으로 전체 프로젝트 현황 표시

### 2. 전체 브리핑

```
> /morning
```

→ 모든 프로젝트 STATE.md 요약 + 우선순위 추천

### 3. 작업 패턴

#### A. 문서/프롬프트 수정

```
> "docs/decisions.md에 D-015 추가"
Claude: 수정 완료

> /sync-all
```

→ 3개 프로젝트 일괄 커밋+푸시 (변경 있는 것만)

#### B. 크로스 프로젝트 검증

```
> "Use orchestrator agent to verify all projects"
```

→ Orchestrator가 의존성 체크, 미커밋 감지, 블로커 추적

#### C. 멀티 프로젝트 동시 작업 (고급)

```bash
# 터미널에서
cd C:\dev\02_ai_config
CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1 claude \
  --add-dir ../01_projects/01_orchestration \
  --add-dir ../01_projects/02_portfolio
```

→ 3개 프로젝트의 CLAUDE.md + skills 모두 로드

### 4. 세션 종료

```
> /sync-all
(세션 닫기)
```

## 핵심 기능

### 계층적 CLAUDE.md

```
C:\dev\CLAUDE.md                    ← 전역 공통 (한국어, Git, 토큰)
  ↓ 재귀 로드
C:\dev\02_ai_config\CLAUDE.md       ← 프로젝트 고유 규칙
```

→ 중복 제거, 토큰 효율 ↑

### Orchestrator Agent (Opus)

- 3개 프로젝트 상태 수집
- 크로스 의존성 체크
- Memory로 패턴 학습

```
> "Use orchestrator agent"
```

### Sync-All Skill

```
> /sync-all
```

→ orchestration + portfolio + ai-config 일괄 커밋+푸시

### 자동화 Hooks

- **SessionStart**: 전체 프로젝트 최근 변경 자동 표시
- **PostToolUse**: 중요 파일 수정 시 `/sync-all` 알림

### Permissions (바운더리)

```json
{
  "deny": [
    "Read(C:/dev/03_evidence/**)",
    "Bash(rm -rf *)",
    "Bash(git push --force*)"
  ],
  "allow": [
    "Read(C:/dev/01_projects/**)",
    "Edit(C:/dev/02_ai_config/docs/**)"
  ]
}
```

## 고급 활용

### Agent Teams (실험적)

```json
// .claude/settings.json에 추가
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

```
> "Enable agent team. Spawn 3:
   1. Review orchestration
   2. Review portfolio
   3. Check cross-dependencies"
```

→ 병렬 검증, 독립 컨텍스트 (높은 비용 주의)

### Headless 파이프라인

```bash
# 모든 프로젝트 상태 JSON 추출
for d in C:/dev/01_projects/*/; do
  cd "$d"
  claude -p "Read STATE.md, output JSON" \
    --output-format json > "/tmp/$(basename $d).json"
done

# 통합 대시보드
jq -s '.' /tmp/*.json | claude -p "Create dashboard"
```

## 문서

- **[daily-workflow.md](docs/daily-workflow.md)**: 일일 워크플로우 가이드
- **[architecture.md](docs/architecture.md)**: 전체 시스템 아키텍처
- **[decisions.md](docs/decisions.md)**: 주요 결정 기록 (D-001 ~ D-014)
- **[claude-code-guide.md](docs/claude-code-guide.md)**: Claude Code 설정 상세

## 관련 프로젝트

| 프로젝트 | GitHub | STATE.md (raw) |
|---------|--------|---------------|
| **Orchestration** | [orchestration](https://github.com/paulseongminpark/orchestration) | [STATE.md](https://raw.githubusercontent.com/paulseongminpark/orchestration/main/context/STATE.md) |
| **Portfolio** | [portfolio_20260215](https://github.com/paulseongminpark/portfolio_20260215) | [STATE.md](https://raw.githubusercontent.com/paulseongminpark/portfolio_20260215/master/context/STATE.md) |
| **AI Config** | [ai-config](https://github.com/paulseongminpark/ai-config) (private) | — |

## 라이선스

Private — 개인 사용 전용
