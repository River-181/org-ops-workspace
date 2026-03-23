---
description: 세션 폴더 + 기획안 + 체크리스트 자동 생성
argument-hint: "[세션번호 또는 날짜] [제목]"
---

# 세션 셋업

## 목적

새 세션의 폴더 구조와 초기 문서를 자동 생성한다.

## 입력

- **세션 식별자**: 조직의 `subfolder_pattern`에 따른 번호 또는 날짜 (예: 04, 260315)
- **세션 제목**: 제목 슬러그 (예: 디지털정리법)

인자가 없으면 사용자에게 식별자와 제목을 물어본다.

## 절차

### 0. 조직 설정 읽기

먼저 `_system/identity/workflow.md`를 읽어 `subfolder_pattern` 값을 확인한다.

패턴 옵션:
- `S{NN}-{title}/` — 클럽·동아리 순번 기반 (예: S04-디지털정리법/)
- `{YYMMDD}-{title}/` — 날짜 기반 (예: 260315-킥오프미팅/)
- `Sprint-{NN}/` — 스타트업 스프린트 (예: Sprint-04/)
- `M{NN}-{YYMMDD}/` — 동아리 모임 번호 (예: M04-260315/)

확인된 패턴으로 폴더명과 파일명 슬러그를 생성한다.

### 1. 중복 확인
`02_sessions/`에서 동일 번호/날짜의 세션 폴더가 있는지 확인한다.
있으면 사용자에게 알리고 중단한다.

### 2. 폴더 생성

`subfolder_pattern`에 따라:

```
02_sessions/{슬러그}/
├── {슬러그}-plan.md
├── {슬러그}-record.md
└── materials/
    └── .gitkeep
```

> **파일명은 폴더명과 동일한 슬러그를 접두사로 붙인다.**
> Quick Switcher·Recent Files에서 세션 번호+주제 모두 보여 구분이 가능하다.

### 3. {슬러그}-plan.md 작성
`01_ops/templates/tpl-세션기획.md` 복사 후 frontmatter 채우기:

```yaml
---
title: "{세션 식별자} 세션 기획안 — {제목}"
kind: session-plan
area: sessions
status: draft
created: {오늘 날짜}
session_id: "{세션 식별자}"
up: "[[MOC-세션]]"
tags:
  - session/plan
---
```

> plan 파일에는 `participants:` 필드를 넣지 않는다.
> 실제 참석자는 세션 종료 후 record 파일에 기록한다.

### 4. {슬러그}-record.md 작성
`01_ops/templates/tpl-세션기록.md` 복사 후 frontmatter 기본값만 채우기:

```yaml
---
title: "{세션 식별자} 세션 기록 — {제목}"
kind: session-record
area: sessions
status: draft
created: {오늘 날짜}
session_id: "{세션 식별자}"
up: "[[MOC-세션]]"
plan: "[[{슬러그}-plan]]"
participants: []
materials: []
tags:
  - session/record
---
```

> 본문은 비워두고, 세션 종료 후 `session-record` 스킬로 채운다.

### 5. backlog 연결
`02_sessions/backlog/`에서 관련 아이디어가 있으면
`{슬러그}-plan.md`의 참고 자료에 `[[wikilink]]`로 연결한다.

### 6. 확인 메시지
생성된 파일 목록을 사용자에게 보여준다.

## 세션 수명주기

```
session-setup (기획 전)
  → {슬러그}-plan.md 작성/수정 (@prep 에이전트)
  → slide-prep (슬라이드 외주 준비)
  → session-launch (D-3 점검)
  → 세션 진행
  → session-record (종료 후)
  → {슬러그}-record.md: participants, 자료, 피드백, 회고
```

## 주의사항

- 세션 폴더명에 공백 금지 — 하이픈 사용
- materials/는 빈 폴더로 생성 (git 추적: `.gitkeep` 추가)
- 파일 이름 변경 시 반드시 `obsidian rename` 사용 — wikilink 자동 업데이트
