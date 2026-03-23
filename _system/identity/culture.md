---
title: "[조직명] — 문화"
status: draft
created: 2026-03-23
kind: system
area: system
tags:
  - system/identity
---

# 소통 문화

> 이 파일은 `/org-init` 스킬이 자동 작성합니다. 직접 편집도 가능합니다.
> `ops`, `publish` 에이전트가 문서 작성 시 어조와 소통 방식을 조정하는 데 사용합니다.

tone: ""                  # 예: 친근함, 전문적, 유머, 격식체
language: ko              # ko | en | ko-en-mixed

# 심리적 분위기
psychological_safety: ""  # 예: 실패 환영, 판단하지 않는 분위기, 자유로운 질문

# 소통 방식
communication_style: ""   # 예: 직접 피드백, 간접 제안, 토론 중심, 투표

# 금기 사항
taboos: []                # 예: [공격적 발언 금지, 개인 SNS 공유 금지, 비밀 준수]

# --- 유형별 선택 필드 ---

# [university-club, club 전용]
opening_ritual: ""        # 모임 시작 의식 (예: 아이스브레이킹, 돌아가며 한마디)
membership_type: ""       # open | closed | invite-only

# [student-council 전용]
formal_tone: false        # true → 공식 회의록 형식 사용

# [startup 전용]
decision_making: ""       # 의사결정 방식 (예: 합의 | 리더 결정 | RFC)
async_sync_ratio: ""      # 비동기:동기 비율 (예: 7:3)

# [sub-team 전용]
escalation_rule: ""       # 상위 조직 에스컬레이션 기준 (언제 보고하는가)

# [university-club 전용]
onboarding_practice: ""   # 신입 환영/온보딩 방식 (예: MT, 선배와 1:1 밥)
