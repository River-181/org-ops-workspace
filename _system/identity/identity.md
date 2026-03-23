---
title: "[조직명] — 조직 정체성"
status: draft
created: 2026-03-23
kind: system
area: system
tags:
  - system/identity
---

# 조직 기본 정보

> 이 파일은 `/org-init` 스킬이 자동 작성합니다. 직접 편집도 가능합니다.
> 모든 에이전트가 부팅 시 이 파일을 읽습니다.

org_name: ""              # 조직명 — 모든 에이전트가 참조 (예: "독서모임 해오름", "SWE팀 알파")
org_type: ""              # 유형: club | university-club | startup | student-project | student-council | sub-team | other
org_topic: ""             # 주제/분야 (예: PKM, 독서, 앱 개발, 심리학)
target_members: ""        # 타겟 멤버 (예: 20대 직장인, ○○대 재학생)
operating_cycle: ""       # 운영 주기 (예: 격주, 매주, 매주 월/목)
size_range: ""            # 인원 규모 (예: 5-15명, 4명) — 에이전트 톤 조정에 사용

# --- 유형별 선택 필드 ---
# 해당 org_type에 맞는 필드만 채우고 나머지는 삭제하거나 비워둡니다.

# [university-club, student-council 전용]
home_institution: ""      # 소속 학교/학과 (예: ○○대학교 경영학과)

# [student-council 전용]
mandate_period: ""        # 임기 (예: 2026-03 ~ 2027-02)
council_generation: ""    # 기수 (예: 제15대)

# [startup 전용]
team_composition: ""      # 역할별 인원 (예: 개발 2, 디자인 1, PM 1)
current_stage: ""         # ideation | mvp | growth | scale

# [student-project 전용]
course_name: ""           # 과목명 (예: 소프트웨어공학)
deadline: ""              # 최종 제출일 (YYYY-MM-DD)

# [sub-team 전용]
parent_org: ""            # 상위 조직명
reporting_to: ""          # 보고 대상 역할명 (예: 팀장, 지도교수)

# [university-club, student-council 전용]
recruitment_cycle: ""     # 모집 주기 (예: 매 학기 초, 연 1회)
