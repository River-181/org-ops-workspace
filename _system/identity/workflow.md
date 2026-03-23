---
title: "[조직명] — 워크플로우"
status: draft
created: 2026-03-23
kind: system
area: system
tags:
  - system/identity
---

# 세션/모임 구조

> 이 파일은 `/org-init` 스킬이 자동 작성합니다. 직접 편집도 가능합니다.
> **`subfolder_pattern` 값이 `02_sessions/` 폴더 구조를 직접 결정합니다.**
> `prep`, `record`, `session-team` 에이전트가 가장 많이 참조합니다.

session_type: ""          # 세션 | 정기모임 | 회의 | 팀미팅 | 스프린트 | 스탠드업 | 팀회의
frequency: ""             # 예: 격주 토요일 오후 2시, 매주 월/목
duration: ""              # 예: 2시간, 1.5시간 (선택)

# 생성 아티팩트 — 각 세션 폴더에 자동 생성할 파일 목록
output_artifacts:
  - plan.md               # 사전 기획 (prep 에이전트 생성)
  - record.md             # 진행 기록 (record 에이전트 생성)
  # 필요 시 추가:
  # - retrospective.md    # 회고 (스타트업/장기 클럽)
  # - minutes.md          # 공식 회의록 (학생회)
  # - task-board.md       # 작업 현황 (팀플/스타트업)

# 세션 폴더 네이밍 패턴 — 아래 옵션 중 하나를 선택하거나 커스텀
# session-setup 스킬이 이 값을 읽어 폴더를 생성합니다.
subfolder_pattern: ""

# 패턴 옵션:
# S{NN}-{title}/          → S01-킥오프/              (클럽/동아리 순번 기반)
# {YYMMDD}-{title}/       → 260315-킥오프미팅/        (날짜 기반, 팀플/학생회)
# Sprint-{NN}/            → Sprint-01/               (스타트업 스프린트)
# M{NN}-{YYMMDD}/         → M01-260315/              (동아리 모임 번호)

# --- 유형별 선택 필드 ---

# [startup 전용]
sprint_length: ""         # 예: 2주

# [student-council 전용]
requires_quorum: false    # 의결 정족수 기록 필요 여부

# [university-club, student-council 전용]
has_semester_cycle: false # 학기별 아카이브 생성 여부

# [student-project 전용]
milestone_dates:          # 중요 일정
  mid_presentation: ""    # YYYY-MM-DD
  final_submission: ""    # YYYY-MM-DD

# [sub-team 전용]
report_format: ""         # 상위 조직 보고 형식
