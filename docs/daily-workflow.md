# 일일 워크플로우

## 아침: 상태 파악

### 1. Claude Code에서 `/morning`

```
> /morning
```

→ 모든 프로젝트 STATE.md 읽고 한줄 요약 출력:
```
오케: 완료 6 / 진행 0 / 막힌것 0 — Phase 1-6 완료
포트: 완료 3 / 진행 2 / 막힌것 1 — Empty House 이미지 작업 중
추천: 포트폴리오 막힌 것 해결 우선
```

### 2. GPT에서 "today"

```
> today
```

→ GPT가 두 STATE URL 읽고 합쳐서 현황 출력
→ tracker 모드로 오늘 할 일 정리

### 3. Obsidian에서 STATE 확인

- `projects/orchestration/STATE.md` 열기
- `projects/portfolio/STATE.md` 열기
- Junction으로 실시간 최신 상태

## 작업 중: AI 협업 루프

### 패턴 A: 단순 실행 (Claude만)

```
사용자 → Claude Code: "OO 수정해"
Claude Code: 수정 → commit → push
사용자 → Claude Code: "/sync"
```

### 패턴 B: 설계 필요 (GPT + Claude)

```
1. GPT에서 토론
   사용자: "이 기능 어떻게 구현하면 좋을까?"
   GPT: 옵션 A vs B 비교 (Canvas)

2. 결정 후 Packet 생성
   GPT: [PACKET] PROJECT=portfolio AGENT=@executor ...

3. 사용자 승인 → Claude Code에 붙여넣기

4. Claude 실행
   Claude: 코드 수정 → commit → push
```

### 패턴 C: 리서치 필요 (Perplexity + Claude)

```
1. Perplexity Space에서 질문
   "Claude Code hooks에서 파일 경로 필터링 하는 법은?"

2. 결과 확인 ([perplexity-research] 형식)

3. Claude 실행 지침을 Claude Code에 전달
```

### 패턴 D: 대량 검증 (Gemini + Claude)

```
1. 코드 전체를 Gemini에 전달
   "이 프로젝트의 전체 구조를 검증해줘"

2. [gemini-review] 결과 확인

3. 실패 항목을 Claude Code로 수정
```

## 작업 후: 기록

### /sync (매 작업 완료 시)

```
> /sync
```

→ STATE.md 갱신 → git commit → auto-push → GitHub Pages 갱신

### Evidence (자동)

- 세션 종료 시 Stop hook이 자동으로 `copy-session-log.py` 실행
- `03_evidence/claude/{project}/{date}.md` 생성

## 멀티AI 핸드오프

### Claude → GPT (설계 토론 필요 시)

```
> /handoff gpt "포트폴리오 네비게이션 구조 재설계 논의"
```

→ 현재 STATE + 요청 내용이 포함된 핸드오프 문서 생성
→ GPT에 붙여넣기

### Claude → Perplexity (리서치 필요 시)

```
> /handoff perplexity "React 19 Suspense 적용 방법"
```

→ 리서치 요청 템플릿 생성
→ Perplexity Space에 붙여넣기

### Claude → Gemini (검증 필요 시)

```
> /handoff gemini "전체 폴더 구조 규칙 준수 검증"
```

→ 검증 체크리스트 포함 핸드오프 문서 생성
→ Gemini에 붙여넣기

## 주간 루틴

| 요일 | 작업 |
|------|------|
| 월 | `/morning`으로 전체 파악 → 주간 목표 설정 |
| 수 | Gemini로 중간 검증 (구조, STATE 정합성) |
| 금 | `/sync` 최종 → STATE에 주간 회고 추가 |

## 트러블슈팅

### "STATE.md가 GitHub Pages에 반영 안 됨"

1. `git log --oneline -1` → 최신 커밋 확인
2. `git push origin main` → 수동 push
3. GitHub repo → Actions 탭 → 빌드 상태 확인
4. 1-2분 대기 (Pages 갱신 지연)

### "Obsidian에서 STATE가 안 보임"

1. Junction 확인: `dir C:\dev\02_ai_config\projects\`
2. Junction이 깨졌으면 재생성:
   ```
   cmd /c mklink /J "대상경로" "원본경로"
   ```

### "Claude Code가 CLAUDE.md를 너무 많이 읽는 것 같다"

1. `/context`로 토큰 사용량 확인
2. 150K+ → `/compact` 또는 `/clear`
3. 글로벌 CLAUDE.md가 4줄인지 확인

## 관련 문서
- [[ai-roles]] — AI별 역할 상세
- [[claude-code-guide]] — Skills, Hooks 상세
- [[git-workflow]] — Git 흐름
