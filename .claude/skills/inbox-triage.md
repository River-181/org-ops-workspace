---
description: 06_inbox 파일 분류 및 라우팅 제안
---

# 인박스 정리

## 목적

`06_inbox/`에 쌓인 미분류 파일을 스캔하고 적절한 목적지 폴더를 제안한다.

## 절차

### 1. 인박스 스캔
- `06_inbox/captures/`와 `06_inbox/triage/`의 모든 파일을 목록화한다.
- README.md는 제외한다.

### 2. 라우팅 판단
- 각 파일의 내용을 읽는다.
- `_system/agents/indexes/260313-폴더-라우팅-인덱스.md`를 참조하여 목적지를 결정한다.
- 키워드 기반 판단:
  - strategy, roadmap → `01_ops/strategy/`
  - meeting, review → `01_ops/meetings/`
  - session, plan → `02_sessions/`
  - brand, logo → `04_studio/brand/`
  - research, method, tool → `05_library/research/`
  - 판단 불가 → `06_inbox/triage/`에 유지

### 3. 제안 표시
- 파일별 제안을 테이블로 표시:

| 파일 | 현재 위치 | 제안 목적지 | 이유 |
|------|----------|-----------|------|

### 4. 사용자 승인 대기
- **직접 이동하지 않는다**. 제안만 하고 사용자 승인을 기다린다.
- 승인 후 파일을 이동하고 필요 시 파일명을 `YYMMDD-제목.md` 형식으로 변환한다.

## 주의사항

- 2주 이상 방치된 파일은 삭제 또는 archive 제안.
- 이미지/에셋 파일은 `04_studio/assets/`로 라우팅.
- 민감 정보가 포함된 파일은 `01_ops/private/`로 라우팅.
