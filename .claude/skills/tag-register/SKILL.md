---
name: tag-register
description: 새 태그를 Tag Registry에 공식 등록합니다. "새 태그 추가", "태그 등록", "이 태그 공식화해줘", tag-audit에서 미등록 태그 발견 후 등록 시 사용. _system/obsidian/tag-registry.md 업데이트 + decisions/ 기록.
argument-hint: "[등록할 태그 또는 개념 설명]"
---

# Tag Register

새 태그 후보를 검토하고 `_system/obsidian/tag-registry.md`에 공식 등록한다.

---

## 승인된 네임스페이스

| 네임스페이스 | 용도 |
|------------|------|
| `ops/` | 운영 관련 |
| `session/` | 세션 관련 |
| `project/` | 프로젝트 관련 |
| `research/` | 리서치·연구 |
| `library/` | 라이브러리 분류 (000~900) |
| `platform/` | 플랫폼·도구 |
| `role/` | 역할·담당 |
| `season/` | 시즌 구분 |
| `type/` | 문서 유형 |
| `system/` | 시스템·설정 |

---

## 워크플로우

### Step 1: 후보 태그 파악

사용자 입력에서 등록할 태그를 파악한다.
명시되지 않은 경우: 설명하는 개념에 맞는 태그를 제안하고 확인 후 진행.

### Step 2: 중복·유사 검사

```bash
# 레지스트리에서 유사 태그 확인
grep -i "키워드" \
  _system/obsidian/tag-registry.md 2>/dev/null

# 볼트 실사용 태그 현황
grep -rh "^\s*- " --include="*.md" \
  01_ops 02_sessions 03_projects 04_studio 05_library \
  | grep "/" | sort | uniq -c | sort -rn | head -30
```

### Step 3: 유효성 검사

1. **네임스페이스 형식** — 승인된 10개 중 하나인가?
2. **의미 중복** — 기존 태그와 실질적으로 같은 개념 아닌가?
3. **명명 규칙** — 소문자 + 하이픈(`kebab-case`)
4. **범용성** — 여러 파일에 실제로 쓰일 태그인가?

검사 실패 시: 대안 제안 후 재확인.

새 **네임스페이스** 자체가 필요한 경우: 사용자 확인 → `update-governance`로 CLAUDE.md 갱신.

### Step 4: tag-registry.md 업데이트

`_system/obsidian/tag-registry.md`가 없으면 신규 생성. 해당 네임스페이스 섹션에 행 추가:

```markdown
## ops/

| 태그 | 네임스페이스 | 설명 | 등록일 |
|------|------------|------|--------|
| `ops/weekly-review` | ops/ | 주간 리뷰 문서 | YYYY-MM-DD |
```

알파벳순 위치에 삽입. 해당 섹션이 없으면 새 섹션 생성.

### Step 5: decisions/ 기록

`_system/agents/memory/decisions/YYMMDD-태그등록-태그명.md` 신규 생성:

```markdown
---
title: 태그 등록 — `네임스페이스/태그명`
status: live
created: YYYY-MM-DD
---

## 결정
`네임스페이스/태그명` 을 Tag Registry에 추가.

## 맥락
[사용자가 제시한 이유]

## 대안
[유사 태그 검토 결과 — 기존 태그 대신 신규 등록한 이유]
```

### Step 6: 완료 확인 및 즉시 적용

등록 완료 후:
1. 등록된 태그 확인 출력
2. 기존 관련 파일에 즉시 적용할지 여부 확인
   - "지금 바로" → Python으로 해당 파일 frontmatter에 태그 추가 (dry-run 먼저)
   - "나중에" → 완료

---

## 주의사항

- 태그 1개당 decisions/ 레코드 1개 생성
- `tag-audit`이 미등록 태그를 발견했을 때 이 스킬로 바로 연계 가능
- 같은 날 여러 태그 등록 시 decisions/ 파일은 태그명별로 분리
