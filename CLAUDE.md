# AI Config 프로젝트

## 역할
- Obsidian 볼트 (문서 중앙 관리)
- 멀티 AI 프롬프트 저장소 (GPT/Gemini/Perplexity)
- 오케스트레이션 시스템 문서

## 구조
```
C:\dev\02_ai_config\
├── docs/           ← 아키텍처, 워크플로우, 결정 기록
├── gpt/            ← GPT 프롬프트 (스냅샷)
├── gemini/         ← Gemini 프롬프트 (스냅샷)
├── perplexity/     ← Perplexity 프롬프트 (스냅샷)
├── claude/         ← Claude 계층 설명
└── projects/       ← Junction (읽기 전용)
```

## 작업 패턴
- 프롬프트 수정 → raw URL 확인
- 문서 수정 → /sync로 커밋
- 주요 결정 → docs/decisions.md에 기록
- 세션 종료 전 → /sync 필수

## 크로스 프로젝트
- orchestration STATE: `C:/dev/01_projects/01_orchestration/context/STATE.md`
- portfolio STATE: `C:/dev/01_projects/02_portfolio/context/STATE.md`
- 프롬프트 변경 시 → 2개 프로젝트 영향도 체크
