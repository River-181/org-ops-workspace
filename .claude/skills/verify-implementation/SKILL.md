---
name: verify-implementation
description: 볼트의 모든 검증 스킬을 순차 실행하여 통합 검증 보고서를 생성합니다. 볼트 정비 후, 대규모 변경 후, 주기적 건강 점검 시 사용. "전체 검증", "볼트 점검", "verify all", "검증 돌려줘" 등의 요청 시 자동 적용.
argument-hint: "[선택사항: 특정 스킬 이름]"
---

# 구현 검증 (Verify Implementation)

[볼트명]에 등록된 모든 검증 스킬을 순차 실행하여 통합 보고서를 생성한다.

---

## 실행 대상 스킬

| # | 스킬 | 설명 |
|---|------|------|
| 1 | `vault-health` | 네이밍·메타데이터·링크·깊이 위생 점검 |
| 2 | `frontmatter-scan` | frontmatter 완성도 통계 — 필드별·폴더별 채택률 |
| 3 | `tag-audit` | 태그 일관성 진단 — 미등록 태그, 네임스페이스 위반 |
| 4 | `link-audit` | 그래프 연결성 진단 — 고아 파일, 단절 군집, up 누락 |

선택 인수 제공 시 해당 스킬만 실행한다.

---

## 워크플로우

### Step 1: 실행 범위 확인

실행할 스킬 목록을 출력한다:

```
## 통합 검증 시작

다음 순서로 실행합니다:
1. vault-health
2. frontmatter-scan
3. tag-audit
4. link-audit
```

### Step 2: 스킬 순차 실행

각 스킬에 대해:

1. `.claude/skills/{스킬명}/SKILL.md` 읽기
2. Workflow 섹션의 검사 단계 수행
3. 이슈 수집 (파일 경로, 문제 설명, 권장 수정)
4. 스킬별 완료 출력:

```
### vault-health 완료
- 검사 항목: N개 / 통과: X개 / 이슈: Y개
```

### Step 3: 통합 보고서

```markdown
## 볼트 검증 보고서 — YYYY-MM-DD

| 검증 스킬 | 상태 | 이슈 수 |
|-----------|------|---------|
| vault-health | PASS / N개 이슈 | N |
| frontmatter-scan | PASS / N개 이슈 | N |
| tag-audit | PASS / N개 이슈 | N |
| link-audit | PASS / N개 이슈 | N |

발견된 총 이슈: X개
```

이슈 발견 시 수정 옵션 제시:
1. **전체 수정** — 모든 권장 수정 자동 적용
2. **개별 수정** — 이슈별 확인 후 적용
3. **건너뛰기** — 보고서만 출력

### Step 4: 수정 후 재검증

수정이 적용된 경우, 이슈가 있던 스킬만 재실행하여 Before/After 비교 출력.

---

## 예외

- 이 스킬 자체는 실행 대상에 포함하지 않는다
- `05_library/seasons/` 아카이브는 스캔에서 제외
- 각 검증 스킬의 자체 예외사항을 준수한다
