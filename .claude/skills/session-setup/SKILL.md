---
name: session-setup
description: 세션 폴더 + 기획안 + 체크리스트 자동 생성
argument-hint: "[세션번호] [제목]"
---

# 세션 셋업

## 목적

새 세션의 폴더 구조와 초기 문서를 자동 생성한다.

## 입력

- **세션 번호**: NN (예: 04)
- **세션 제목**: 한글 제목 (예: 디지털정리법)

인자가 없으면 사용자에게 번호와 제목을 물어본다.

## 절차

### 1. 중복 확인
`02_sessions/`에서 동일 번호의 세션 폴더가 있는지 확인한다.
있으면 사용자에게 알리고 중단한다.

### 2. 폴더 생성

```
02_sessions/S{NN}-{제목슬러그}/
├── S{NN}-{제목슬러그}-plan.md
├── S{NN}-{제목슬러그}-record.md
└── materials/
    └── .gitkeep
```

> **파일명은 폴더명과 동일한 슬러그를 접두사로 붙인다.** (예: 폴더 `S04-디지털정리법/` → 파일 `S04-디지털정리법-plan.md`)
> Quick Switcher·Recent Files에서 세션 번호+주제 모두 보여 구분이 가능하다.

### 3. S{NN}-{제목슬러그}-plan.md 작성
`01_ops/templates/tpl-세션기획.md` 복사 후 frontmatter 채우기:

```yaml
---
title: "S{NN} 세션 기획안 — {제목}"
kind: session-plan
area: sessions
status: draft
created: {오늘 날짜}
session_id: "S{NN}"
up: "[[MOC-세션]]"
tags:
  - session/S{NN}
  - session/plan
---
```

> plan 파일에는 `participants:` 필드를 넣지 않는다.
> 실제 참석자는 세션 종료 후 record 파일에 기록한다.

### 4. S{NN}-{제목슬러그}-record.md 작성
`01_ops/templates/tpl-세션기록.md` 복사 후 frontmatter 기본값만 채우기:

```yaml
---
title: "S{NN} 세션 기록 — {제목}"
kind: session-record
area: sessions
status: draft
created: {오늘 날짜}
session_id: "S{NN}"
up: "[[MOC-세션]]"
plan: "[[S{NN}-{제목슬러그}-plan]]"
participants: []
materials: []
tags:
  - session/S{NN}
  - session/record
---
```

> 본문은 비워두고, 세션 종료 후 `session-record` 스킬로 채운다.

### 5-a. Obsidian CLI로 파일 title 설정

파일 생성 후 Obsidian이 탭에서 `title:` 값을 표시하도록 property 확인:

```bash
obsidian vault="[볼트명]" property:set name="title" \
  value="S{NN} 세션 기획안 — {제목}" \
  path="02_sessions/S{NN}-{제목슬러그}/S{NN}-{제목슬러그}-plan.md" silent

obsidian vault="[볼트명]" property:set name="title" \
  value="S{NN} 세션 기록 — {제목}" \
  path="02_sessions/S{NN}-{제목슬러그}/S{NN}-{제목슬러그}-record.md" silent
```

### 5. backlog 연결
`02_sessions/backlog/`에서 관련 아이디어가 있으면
`S{NN}-plan.md`의 참고 자료에 `[[wikilink]]`로 연결한다.

### 6. 확인 메시지
생성된 파일 목록을 사용자에게 보여준다.

## 세션 수명주기

```
session-setup (기획 전)
  → S{NN}-{슬러그}-plan.md 작성/수정 (@prep 에이전트)
  → slide-prep (슬라이드 외주 준비)
  → session-launch (D-3 점검)
  → 세션 진행
  → session-record (종료 후)
  → S{NN}-{슬러그}-record.md: participants, 자료, 피드백, 회고
```

## 주의사항

- 세션 폴더명에 공백 금지 — 하이픈 사용
- materials/는 빈 폴더로 생성 (git 추적: `.gitkeep` 추가)
- 파일명은 반드시 `S{NN}-{제목슬러그}-plan.md` 형식 — 번호만 있는 `S{NN}-plan.md` 금지
- 파일 이름 변경 시 반드시 `obsidian rename` 사용 — wikilink 자동 업데이트
