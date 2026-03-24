---
name: weekly-review
description: 볼트 전체 스캔 → 주간 현황 보고 생성
---

# 주간 리뷰

## 목적

볼트 전체 상태를 스캔하여 운영팀이 3분 안에 현황을 파악할 수 있는 주간 리뷰 노트를 생성한다.

## 절차

### 1. 컨텍스트 로드
- `_system/agents/memory/active-context/`의 최신 파일을 읽는다.
- 이전 주간 리뷰가 있으면 `01_ops/reviews/`에서 최신 것을 읽는다.

### 2. 볼트 스캔
- 모든 폴더의 .md 파일 목록을 수집한다.
- frontmatter `status:` 필드로 상태별 분포 집계 (draft / live / archived).
- `06_inbox/` 미분류 파일 수를 확인한다.
- `02_sessions/`에서 다음 세션 준비 상태를 점검한다:
  - plan.md 존재 여부
  - materials/ 내용물
  - record.md 상태
- `01_ops/posts/`에서 발행 대기 항목을 확인한다.

### 3. 리뷰 노트 생성
- 템플릿: `01_ops/templates/tpl-주간리뷰.md`
- 출력: `01_ops/reviews/YYMMDD-주간리뷰.md`
- 내용:
  - 이번 주 성과 (새로 생성·완료된 문서)
  - 이번 주 이슈 (오래된 draft, 빈 폴더, 미분류 항목)
  - 다음 주 계획 (에이전트별 추천 작업)

### 4. 메모리 갱신
- `_system/agents/memory/active-context/YYMMDD-상태요약.md` 갱신한다.

## 주의사항

- `05_library/seasons/` 아카이브는 스캔에서 제외한다.
- 파일 수만 세지 말고 질적 판단을 포함한다.
- 운영팀 액션으로 이어지는 구체적 제안을 한다.
