---
title: "[조직명] — 플랫폼"
status: draft
created: 2026-03-23
kind: system
area: system
tags:
  - system/identity
---

# 플랫폼 역할 정의

> 이 파일은 `/org-init` 스킬이 자동 작성합니다. 직접 편집도 가능합니다.
> `publish`, `ops` 에이전트가 외부 플랫폼 작업 시 참조합니다.
> 이 파일은 일부 조직(팀플, 하위팀)에서는 불필요할 수 있습니다.

local_obsidian:
  role: source-of-truth   # 변경 불가 — 모든 원본 문서는 여기에
  notes: ""

# 아래는 조직에 맞게 설정

platform_primary:         # 주요 소통 플랫폼
  name: ""                # 예: KakaoTalk | Discord | Slack | Line | WeChat
  role: ""                # 예: 실시간 소통, 공지, 질문

platform_collaboration:   # 협업/공유 플랫폼 (선택)
  name: ""                # 예: Notion | Google Drive | GitHub | Confluence
  role: ""                # 예: 공유 대시보드, 파일 공유, 위키

platform_publishing:      # 외부 발행 채널 (선택)
  name: ""                # 예: Instagram | Notion Public | GitHub Pages | 학과 홈페이지
  role: ""                # 예: 공개 홍보, 외부 공지

# 추가 플랫폼 필요 시 자유 추가
# platform_extra:
#   name: ""
#   role: ""

# 원칙: 로컬에서 작성 → 외부에 요약/링크 게시. 역방향 금지.
