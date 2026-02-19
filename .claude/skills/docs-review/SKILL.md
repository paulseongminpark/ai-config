---
name: docs-review
description: C:/dev 전체 볼트 Living Docs 점검 — stale 감지 및 업데이트 방향 제안
user-invocable: true
allowed-tools: Bash, Read
model: sonnet
---

## 목적

C:/dev 볼트의 .md 문서들이 실제 구현(scripts, hooks, skills, settings.json)과
일치하는지 점검한다. **수정은 하지 않음** — 리포트만 출력하고, 사용자가 별도로 진행.

## Steps

1. `docs-review.sh` 실행 → 리포트 수신
2. 리포트 분석 → STALE 항목에 대해 구체적 업데이트 방향 제안
3. 우선순위 제시 (영향도 높은 것 먼저)

## 실행

```bash
bash /c/Users/pauls/.claude/scripts/docs-review.sh
```

## Output 해석

| 섹션 | 의미 | 권장 조치 |
|------|------|----------|
| STALE | doc 수정일 < 소스 최신 커밋일 | 해당 doc 업데이트 |
| PLAN 상태 미표기 | docs/plans/ 내 COMPLETED 미표기 | COMPLETED 표기 또는 검토 |
| GIT 미추적 | 추적되지 않는 .md 파일 | git add 또는 의도적 제외 확인 |
| CURRENT ✅ | 최신 상태 | 조치 불필요 |

## 수정 완료 후

```
/sync-all
```

모든 repo 일괄 커밋+푸시.

## 운영 주기

주 1회 (금요일 세션 종료 전 또는 월요일 시작 시) 권장.
