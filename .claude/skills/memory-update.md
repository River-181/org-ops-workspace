---
description: 에이전트 메모리(active-context, change-log, decisions) 갱신
---

# 메모리 갱신

## 목적

현재 세션의 작업 결과를 에이전트 메모리에 기록하여 다음 세션에서 컨텍스트를 이어갈 수 있게 한다.

## 절차

### 1. 현재 메모리 읽기
- `_system/agents/memory/active-context.md`를 읽는다.
- 없으면 초기 상태로 간주한다.

### 2. 변경사항 수집
- 이번 세션에서 생성/수정/삭제된 파일을 파악한다.
- 구조적 결정이 있었는지 확인한다.

### 3. Active Context 갱신
- `_system/agents/memory/active-context.md` 전체를 새로 작성한다 (**덮어쓰기**):
  ```yaml
  ---
  title: Active Context
  status: live
  created: YYYY-MM-DD
  ---
  ```
  - 현재 볼트 상태 한 줄 요약
  - 진행 중인 작업 목록
  - 현재 우선순위
  - 주의점/블로커
  - 다음 세션 정보 (알고 있으면)

### 4. Change Log 기록
- `_system/agents/memory/change-log.md` 최상단에 오늘 날짜 섹션을 **append**:
  ```markdown
  ## YYYY-MM-DD
  
  - {변경된 내용 요약}
  - {변경 이유}
  - {영향 범위}
  ```

### 5. Decision Log (해당 시)
- 구조적 결정이 있었으면 `_system/agents/memory/decisions/YYMMDD-결정제목.md` 작성:
  - 결정 사항
  - 맥락 (왜?)
  - 대안 (고려했지만 선택하지 않은 것)
  - 후속 조치

## 주의사항

- active-context는 항상 최신 상태로 **덮어쓰기** — 이전 버전은 필요 없음.
- change-log는 간결하게. 요약 + 한 줄 이유.
- 모든 decisions 파일은 `YYMMDD-제목.md` 네이밍을 따른다.
