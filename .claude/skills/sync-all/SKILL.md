---
name: sync-all
description: 모든 프로젝트 STATE.md 동기화 및 커밋. ai-config 포함.
context: fork
agent: general-purpose
model: opus
---

# Sync All Projects

모든 프로젝트의 변경사항을 커밋+푸시합니다.

## 실시간 상태

### Orchestration
```bash
!cd C:/dev/01_projects/01_orchestration && git status -s && echo "---" && git log --oneline -1
```

### Portfolio
```bash
!cd C:/dev/01_projects/02_portfolio && git status -s && echo "---" && git log --oneline -1
```

### AI Config
```bash
!cd C:/dev/02_ai_config && git status -s && echo "---" && git log --oneline -1
```

## 작업 절차

1. **각 프로젝트별로**:
   - 변경된 파일 확인 (git status)
   - STATE.md 있으면 읽기 → 최근 작업 확인
   - 적절한 커밋 메시지 생성: "[project] 한줄 설명"
   - git add + commit + push

2. **검증**:
   - push 성공 확인
   - GitHub Pages 갱신 안내 (1-2분 소요)

3. **보고**:
   ```
   DONE:
   - orchestration: [커밋 메시지]
   - portfolio: [커밋 메시지]
   - ai-config: [커밋 메시지]

   NEXT: GitHub Pages 갱신 대기 (~1분)
   ```

## 주의사항
- orchestration: main 브랜치
- portfolio: master 브랜치
- ai-config: main 브랜치
- force push 절대 금지
