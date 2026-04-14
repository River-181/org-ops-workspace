---
name: memory-update
description: 에이전트 메모리(active-context, change-log, decisions) 갱신. 세션 작업 결과를 메모리에 반영하는 일반 절차. "메모리 갱신", "컨텍스트 업데이트", "오늘 작업 기록해줘" 등의 요청 시 사용. 세션 종료 후 자동 적용.
---

# 메모리 갱신

현재 세션의 작업 결과를 에이전트 메모리에 반영하여 다음 세션에서 컨텍스트를 이어갈 수 있게 한다.

> `update-governance`와의 분업:
> - `memory-update` — 세션 작업 결과를 메모리에 반영하는 **일반 절차**
> - `update-governance` — "이 변경을 어디에 써야 하나"가 불명확할 때 **라우팅 판단**

---

## 메모리 구조

```
_system/agents/memory/
├── active-context.md   ← 클럽 현재 상태 스냅샷 (덮어쓰기)
├── change-log.md       ← 변경 이력 (날짜별 append)
├── decisions/          ← 구조적 결정 (개별 파일)
└── MEMORY.md           ← 검증된 패턴·교훈 장기기억 (week-promote가 관리)
```

---

## 절차

### 1. 변경사항 수집

이번 세션에서:
- 생성·수정·삭제된 파일 목록
- 내린 결정 (대안 비교가 있었던 것)
- 다음 세션으로 넘기는 작업

### 2. active-context.md 덮어쓰기

`_system/agents/memory/active-context.md`를 현재 상태로 **전체 교체**:

```markdown
---
title: 현재 상태 요약
kind: context
area: system
status: live
created: YYYY-MM-DD
updated: YYYY-MM-DD
up: "[[MAP-에이전트]]"
---

# Active Context

## 클럽 현황
[현재 상태 한 줄]

## 진행 중인 작업
- [ ] 작업1 (담당자, 마감)

## 우선순위
1.

## 블로커
(없으면 생략)

## 다음 세션
[다음에 할 일 1~2줄]
```

### 3. change-log.md append

`_system/agents/memory/change-log.md`에 섹션 **추가** (기존 항목 위에 삽입):

```markdown
## YYYY-MM-DD — [작업 제목]

- [변경 내용 — 파일 경로 포함]
- 이유: [한 줄]
```

### 4. decisions/ 기록 (해당 시)

대안 비교가 있었던 구조적 결정은 `_system/agents/memory/decisions/YYMMDD-결정제목.md` 신규 생성:

```markdown
---
title: 결정 제목
status: live
created: YYYY-MM-DD
---

## 결정
[한 문장]

## 맥락
[왜 이 결정을 했는가]

## 대안
[고려했지만 선택하지 않은 것과 이유]

## 후속 조치
[이 결정으로 해야 할 일]
```

---

## 주의사항

- `active-context.md`는 **전체 교체** — append 금지
- `change-log.md`는 **append 전용** — 기존 내용 수정 금지
- `MEMORY.md`는 이 스킬이 직접 수정하지 않는다 — `week-promote` 스킬이 담당
- 일상적 변경은 change-log로 충분. decisions/는 대안 비교가 있을 때만
