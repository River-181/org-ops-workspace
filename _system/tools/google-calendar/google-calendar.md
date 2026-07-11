---
title: Google Calendar
kind: tool
area: system
status: live
created: 2026-07-08
updated: 2026-07-08
tags:
  - system/tools
  - ops/calendar
tool_id: google-calendar
tool_type: calendar
auth_mode: Google OAuth / ICS handoff
owner: ops
verified_on: 2026-07-08
up: "[[MAP-도구]]"
---

# Google Calendar

> [클럽명]의 회원 공유 일정과 운영팀 내부 일정을 관리하는 캘린더 레이어.

## 역할

| 캘린더 | 대상 | 역할 |
|---|---|---|
| [클럽명] Club Calendar | 회원 + 운영팀 | 세션, 공개 모집, 공개 가능한 마감 |
| [클럽명] Ops Calendar | 운영팀 | 운영회의, 준비 마감, 예약/공지/확인 |

## 운영 원칙

- Obsidian [볼트명]이 일정 근거의 Source of Truth다.
- Google Calendar는 구독·알림·모바일 확인을 위한 실행 레이어다.
- Notion은 캘린더 원본이 아니라 월간 일정 대시보드/설명 페이지로 사용한다.
- Discord에는 주요 일정 공지와 리마인더만 올린다.
- 민감한 운영 내용, 멤버 개인정보, 내부 판단은 캘린더 설명에 넣지 않는다.

## HAL 작업 방식

1. [볼트명]에서 일정 근거 노트를 읽는다.
2. 회원 공개 가능 / 운영팀 전용 / 제외·보류를 분리한다.
3. Google Calendar API 연결 전에는 `.ics` 파일을 생성한다.
4. API 연결 후에는 운영자 승인 후 실제 캘린더에 생성·수정한다.
5. 변경 사항은 `01_ops/calendar/`와 change-log에 남긴다.

## 인증 상태

- Google Workspace OAuth 미연결 시 직접 쓰기 전에는 [운영자] 계정의 OAuth 연결 또는 사람이 `.ics`를 import해야 한다.
- `.ics` 파일 생성 위치: `01_ops/calendar/`
