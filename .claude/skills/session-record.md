---
description: 세션 종료 후 전체 마무리 — record.md 작성, 파일 정리, 메모리 갱신
argument-hint: "[세션 폴더 경로] (예: 02_sessions/S00-OT)"
---

# session-record

## 목적

세션이 끝난 뒤 **3단계 마무리**를 한 번에 완료한다:

1. `{슬러그}-record.md` 내용 채우기
2. 세션 폴더 파일 정리 (이름, title property, status)
3. 에이전트 메모리 갱신 (active-context, change-log)

record.md는 **실제 일어난 것**의 기록이며, plan.md(설계)와 구분된다.

---

## record.md vs plan.md

| | {슬러그}-plan.md | {슬러그}-record.md |
|-|---------|-----------|
| 성격 | 설계·의도 | 실제·결과 |
| participants | ✗ | ✓ (실제 참석자) |
| 자료 링크 | 초안 | 실제 사용된 것 |
| 피드백 | ✗ | ✓ |
| 회고 | ✗ | ✓ |

---

## 절차

### 1. 세션 폴더 파악

직접 경로 확인: `02_sessions/{슬러그}/`

기대 구조:
```
{슬러그}/
├── {슬러그}-plan.md      ← 기획안 (이미 존재)
├── {슬러그}-record.md    ← 오늘 채울 파일
└── materials/
    ├── 슬라이드.md
    └── 스크립트.md
```

`{슬러그}-record.md`가 없으면 `01_ops/templates/tpl-세션기록.md` 복사해 생성.

---

### 2. {슬러그}-record.md Frontmatter 채우기

```yaml
---
title: "{세션 식별자} 세션 기록 — {제목}"
kind: session-record
area: sessions
status: live
created: {세션 날짜}
session_id: "{세션 식별자}"
up: "[[MOC-세션]]"
plan: "[[{슬러그}-plan]]"
participants:
  - "(참석자 이름)"
  - ...
materials:
  - "[[materials/슬라이드]]"
tags:
  - session/record
---
```

> **participants는 record 파일에만 존재한다.** plan 파일에는 넣지 않는다.
> `plan:` 링크는 반드시 `"[[{슬러그}-plan]]"` — 슬러그 그대로.

---

### 3. 본문 섹션 채우기

**발표 / 자료 섹션**
- 파일 공유 링크, materials/ 파일 wikilink, 외부 URL 모두 기록
- 자료 형식 (슬라이드/핸드아웃/영상) 명시

**실제 진행 내용**
- plan 대비 변경된 항목은 "(계획 대비 변경)" 표시
- 예상치 못한 토론, 시간 초과, 생략 항목 포함

**피드백**
- 수집 내용 정리
- 익명 의견: 내용만 / 기명 의견: `[[이름]]` 링크 사용 가능
- 수집 없으면 "수집 안 됨" 명시

**회고**
- 운영진 함께 작성 권장
- **수치 지표 테이블** 반드시 채우기 — 시즌 말 aggregate에 사용

---

### 4. 파일 정리 (Obsidian CLI)

#### 4-1. plan 파일 status → archived

```bash
# 세션이 완료됐으므로 plan도 archived
obsidian vault="볼트이름" property:set name="status" value="archived" \
  path="02_sessions/{슬러그}/{슬러그}-plan.md" silent
```

#### 4-2. record.md title property 확인

```bash
obsidian vault="볼트이름" property:set name="title" \
  value="{세션 식별자} 세션 기록 — {제목}" \
  path="02_sessions/{슬러그}/{슬러그}-record.md" silent
```

---

### 5. 에이전트 메모리 갱신

#### 5-1. change-log.md append

`_system/agents/memory/change-log.md` 맨 위에 오늘 날짜 섹션 추가:

```markdown
## YYYY-MM-DD

- {세션 식별자} 세션 완료
  - 참가자: {N}명
  - 주요 내용: {한 줄 요약}
  - 다음 액션: {있으면 기재}
```

#### 5-2. active-context.md 갱신

`_system/agents/memory/active-context.md` 에서:
- 세션 상태를 "완료"로 변경
- 다음 세션 또는 후속 액션 반영
- 전체 파일 **덮어쓰기** (항상 최신 상태만 유지)

---

## 완성 기준

- [ ] frontmatter: title, participants, plan, session_id, materials, status: live 채워짐
- [ ] 발표 자료 링크 모두 기록
- [ ] 피드백 섹션 존재 (없으면 "수집 안 됨")
- [ ] 회고 섹션 + 수치 지표 채워짐
- [ ] plan 파일 status → archived
- [ ] change-log.md에 날짜 섹션 추가됨
- [ ] active-context.md 갱신됨

---

## 주의사항

- 회고는 세션 당일 또는 다음 날까지 — 기억이 흐릿해지기 전에
- 피드백에 개인 식별 정보 포함 금지
- materials/ 파일이 아직 없으면 링크만 걸고 나중에 생성

---

## 세션 수명주기

```
session-setup → {슬러그}-plan.md 작성 → 세션 진행 → session-record
```

관련 스킬: `session-setup`, `slide-prep`, `session-launch`, `memory-update`
