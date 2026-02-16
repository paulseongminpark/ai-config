# 주요 결정 기록

> 모든 결정은 이유가 있다. 나중에 "왜 이렇게 했지?" 할 때 이 문서를 본다.

## D-001: SoT를 Obsidian에서 Git으로 전환

**결정**: Obsidian 볼트 대신 Git repo의 `context/STATE.md`를 단일 진실 소스로.

**이유**:
- Obsidian은 로컬 전용. 다른 AI가 접근 불가.
- Git → GitHub Pages로 URL 공개 가능.
- Claude Code가 Git을 직접 조작 가능.
- 버전 히스토리가 자동으로 남음.

**대안 (폐기)**:
- Obsidian + Git sync 플러그인 → 충돌 위험, 양방향 동기화 복잡
- Google Docs → AI 접근 가능하지만 구조화 어려움

**영향**: 모든 AI가 동일한 STATE를 읽을 수 있게 됨.

## D-002: Obsidian을 읽기 전용 뷰어로

**결정**: Obsidian에서 STATE.md를 편집하지 않는다. Junction point로 읽기만.

**이유**:
- 편집 권한은 Claude Code에만 → SoT 충돌 방지
- Junction은 실시간 반영 → git pull 불필요
- Obsidian의 강점(그래프 뷰, 링크)은 읽기에서도 활용 가능

**트레이드오프**: Obsidian에서 빠르게 메모하고 싶을 때 불편. 하지만 SoT 일관성이 더 중요.

## D-003: Jeff Su 방법론 채택

**결정**: 폴더 구조에 Jeff Su의 5가지 규칙 적용.

**규칙**:
1. 5레벨 MAX
2. 2자리 넘버링
3. 99 = Archive
4. 명확한 이름
5. 프로젝트 = 독립 단위

**이유**:
- 간단한 규칙 → 따르기 쉬움 → 일관성 유지
- 넘버링 → 자연 정렬 → 탐색 용이
- Archive → 삭제 대신 보관 → 안전

**대안 (폐기)**:
- PARA 방법론 (Projects/Areas/Resources/Archives) → 프로젝트와 리소스 구분이 모호
- 날짜 기반 구조 → 프로젝트 간 이동이 불편

## D-004: LOGS/TODAY 폐기, STATE.md에 통합

**결정**: 중앙 LOGS 폴더와 TODAY 파일을 폐기. STATE.md가 항상 최신 상태를 반영.

**이유**:
- LOGS를 읽는 행위 자체가 토큰 낭비
- TODAY 파일이 매일 쌓여 관리 부담
- STATE.md 하나만 보면 현재 상태를 알 수 있어야 함

**이전 문제**:
- "오늘 뭐 했지?" → LOGS 읽기 → 토큰 소모
- "다음 할 일은?" → STATE + TODAY 둘 다 봐야 함
- 정보가 분산되면 어느 것이 맞는지 혼란

**해결**: STATE.md에 완료/진행/다음/막힌것 섹션을 두고, `/sync`로 갱신.

## D-005: 글로벌 CLAUDE.md를 4줄로 축소

**결정**: 146줄 → 4줄. 나머지는 rules/와 프로젝트별로 분리.

**이유**:
- 매 턴 2000토큰 → 100토큰 (95% 절감)
- 20턴 세션 기준 38K 토큰 절약
- 프로젝트와 무관한 규칙이 항상 로드되던 문제 해결

**수치**:
```
이전: 146줄 × 매 턴 = ~40K/세션
이후: 4줄 + rules 2개 + 프로젝트 CLAUDE = ~22K/세션
절약: ~18K/세션 (실질적으로 2-3턴 추가 작업 가능)
```

## D-006: .claudeignore 대신 permissions.deny

**결정**: `.claudeignore` 파일을 사용하지 않는다.

**이유 (Perplexity 교차검증 결과)**:
- `.claudeignore`는 우회 가능한 소프트 제한
- `permissions.deny`는 엔진 레벨 하드 제한
- 보안이 필요한 파일(.env 등)은 하드 제한이 적절

**설정**:
```json
"deny": [
  "Read(.env*)",
  "Read(C:\\dev\\03_evidence/**)",
  "Bash(rm -rf *)",
  "Bash(git push --force*)"
]
```

## D-007: Skills를 .claude/commands/ 대신 .claude/skills/ 사용

**결정**: 구식 commands 디렉토리 대신 skills 사용.

**이유 (Perplexity 교차검증)**:
- `.claude/commands/*.md`는 구식 포맷
- `.claude/skills/<name>/SKILL.md`가 현재 권장
- SKILL.md frontmatter로 메타데이터(model, agent, context) 지정 가능

## D-008: Obsidian에서 Symlink 대신 Junction 사용

**결정**: `mklink /D` (symlink) 대신 `mklink /J` (junction) 사용.

**이유**:
- Symlink은 Windows에서 관리자 권한 필요
- Junction은 일반 사용자도 생성 가능
- 기능적으로 동일 (디렉토리 레벨 링크)

**명령**:
```powershell
cmd /c mklink /J "대상" "원본"
```

## D-009: Evidence는 Git 추적 안 함

**결정**: `03_evidence/`는 로컬 전용. Git에 올리지 않음.

**이유**:
- 세션 로그는 개인 기록 (공개 불필요)
- 파일 크기가 클 수 있음 (Git 저장소 비대화)
- 민감한 대화 내용 포함 가능

**백업**: 수동으로 필요 시 외부 저장소에 복사.

## D-010: GitHub repo portfolio_ui_test_v2 → portfolio_20260215 rename

**결정**: repo 이름을 날짜 기반으로 변경. 구 `portfolio` repo는 archive.

**이유**:
- `portfolio_ui_test_v2`는 임시 명명 (테스트 버전 느낌)
- `portfolio`는 이미 구 repo가 점유
- 날짜 기반 이름으로 버전 명확화

**영향**:
- GitHub Pages URL 변경: `portfolio_ui_test_v2` → `portfolio_20260215`
- 모든 AI 프롬프트의 URL 업데이트 필요 (완료)

## D-011: 자동화는 2가지만

**결정**: post-commit auto-push와 Stop evidence 백업만 자동화.

**이유**:
- 복잡한 자동화 = 디버깅 지옥
- "마법"보다 "예측 가능"이 중요
- 나머지는 `/sync`, `/handoff` 등 명시적 호출

**폐기한 자동화 아이디어**:
- ~~파일 변경 시 자동 커밋~~ → 너무 잦은 커밋
- ~~토큰 임계값 알림~~ → Hook 미지원
- ~~세션 시작 시 자동 STATE 읽기~~ → 토큰 낭비

## D-012: GitHub Pages URL → raw.githubusercontent.com

**결정**: STATE.md 접근을 GitHub Pages 대신 raw.githubusercontent.com 사용.

**이유**:
- GPT가 GitHub Pages URL을 10번 중 8번 읽기 실패
- raw URL이 직접 파일 접근으로 더 안정적
- 캐싱 이슈 없음

**변경**:
- 오케: `https://raw.githubusercontent.com/paulseongminpark/orchestration/main/context/STATE.md`
- 포트: `https://raw.githubusercontent.com/paulseongminpark/portfolio_20260215/master/context/STATE.md`

**영향**: 11개 프롬프트 파일 일괄 업데이트 (2026-02-16)

## D-013: 계층적 CLAUDE.md + ai-config .claude/ 추가

**결정**: C:\dev\CLAUDE.md (공통) + 각 프로젝트 CLAUDE.md (개별) 하이브리드 구조.

**이유**:
- CLAUDE.md는 부모 디렉토리도 재귀 로드 (settings.json과 달리)
- 공통 규칙 중복 제거 → 토큰 절약
- ai-config도 문서 수정 시 /sync 필요 → .claude/ 추가

**구조**:
```
C:\dev\CLAUDE.md                  # 모든 프로젝트 공통 (한국어, Git 규칙)
├── 01_projects\01_orchestration\
│   ├── CLAUDE.md                 # 프로젝트 고유
│   └── .claude\                  # permissions, hooks, skills
├── 01_projects\02_portfolio\
│   └── (동일)
└── 02_ai_config\
    ├── CLAUDE.md                 # 문서 관리 규칙
    └── .claude\                  # ← 신규 추가
        ├── settings.json         # model: opus, permissions
        ├── agents\orchestrator.md # 멀티 프로젝트 검증
        └── skills\sync-all\      # 전체 동기화
```

**새 기능**:
- SessionStart 훅: 모든 프로젝트 최근 변경 자동 로드
- PostToolUse 훅: 중요 파일 수정 시 /sync 알림
- Orchestrator agent: 크로스 프로젝트 의존성 체크 (opus)
- /sync-all 스킬: 3개 프로젝트 일괄 커밋+푸시

**영향**: ai-config에서도 /sync 사용 가능, 세션 시작 시 전체 현황 자동 표시

## D-014: 엣지한 오케스트레이션 시스템 (Opus + Agents + Teams)

**결정**: Claude Code 고급 기능을 활용한 프로덕션급 오케스트레이션 시스템 구축.

**핵심 구성**:

1. **계층적 설정 (Settings Hierarchy)**
   - C:\dev\CLAUDE.md: 전역 공통 규칙 (모든 프로젝트 자동 로드)
   - 프로젝트별 CLAUDE.md: 개별 오버라이드
   - .claude/settings.json: permissions + hooks (프로젝트 고유)
   - .claude/rules/: 조건부 규칙 (경로 기반)

2. **Orchestrator Agent (opus + memory)**
   - 3개 프로젝트 상태 수집 및 크로스 검증
   - 의존성 체크, 미커밋 감지, 장기 블로커 추적
   - memory로 패턴 학습 및 세션 간 인사이트 축적

3. **자동화 Hooks**
   - SessionStart: 전체 프로젝트 최근 변경 자동 표시
   - PostToolUse: 중요 파일 수정 시 /sync 알림
   - PreToolUse: STATE.md 직접 편집 차단 (계획, 미구현)
   - Stop: 품질 게이트 (계획, 미구현)

4. **Skills**
   - /sync-all: 3개 프로젝트 일괄 커밋+푸시 (opus, fork context)
   - /morning: 전체 브리핑 (이미 존재, haiku, Explore agent)

5. **Permissions (바운더리)**
   - deny: evidence 디렉토리, .env, rm -rf, force push, curl
   - allow: 프로젝트 소스, git 읽기 명령, 안전한 git 쓰기

6. **Agent Teams (실험적, 선택)**
   - 병렬 크로스 프로젝트 검증
   - 독립 컨텍스트 윈도우로 토큰 오염 방지
   - 높은 비용 주의 → 주간 검증에만 사용

**설계 원칙**:
- **토큰 효율**: 계층적 로드로 중복 제거, haiku/sonnet/opus 선택적 사용
- **바운더리 강제**: permissions.deny가 allow보다 우선 (하드 제한)
- **실시간 동기화**: SessionStart 훅으로 세션 시작 시 자동 컨텍스트 주입
- **자율 학습**: Orchestrator의 memory로 패턴 축적

**트레이드오프**:
- 장점: 완전 자동화, 크로스 검증, 확장 가능
- 단점: 초기 설정 복잡도, 훅 디버깅 어려움

**참고**: Claude Code 공식 문서 (Settings, Agents, Teams, Hooks, Permissions, Best Practices)

## 관련 문서
- [[philosophy]] — 결정의 배경 철학
- [[architecture]] — 결정이 반영된 구조
- [[claude-code-guide]] — 기술적 구현
