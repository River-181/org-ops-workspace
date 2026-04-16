---
title: MAP-에이전트
kind: map
area: system
status: live
created: 2026-03-19
updated: 2026-03-28
up: "[[MOC-시스템]]"
tags:
  - type/map
  - system/agent
---

# MAP-에이전트

> [볼트명] 에이전트 시스템의 동적 인덱스.
> 설명은 상단에, 전체 문서 분포는 Base에, 긴 연결 문서는 접힌 섹션에 둔다.

## 핵심

- [[260311-[클럽명]-OS-에이전트아키텍처|에이전트 아키텍처 전체 설계]]
- [[_system/agents/teams/README|팀 운영 가이드]]
- [[active-context]]
- [[change-log]]
- [[MEMORY|장기기억 (MEMORY.md)]]
- [[260313-폴더-라우팅-인덱스]]
- [[260313-파일명-코드-참조규칙]]

## 뷰

![[agents.base#kind별]]

![[agents.base#상태별]]

## 가이드

- 현재 동작 기준 문서는 `active-context`, 구조 기준 문서는 아키텍처와 프로토콜 문서다. (슬림화 원칙 — 핵심 상태만 유지, 상세 이력은 `change-log`로 분리)
- 폴더별 분포나 누락을 볼 때만 Base로 내려가고, 실제 실행 규칙은 프로토콜 문서를 읽는다.
- 레거시 프로필과 로그는 그래프 보존용으로 아래에 접어 둔다.

## 상세

> [!abstract]- 그래프 링크
> ### 프로토콜
> - [[260313-에이전트-워크스페이스-운영-프로토콜]]
> - [[260316-에이전트간-위임-프로토콜]]
> - [[260316-다인운영-협업규칙]]
> - [[260316-워크스페이스OS-업그레이드-프로토콜]]
> - [[260321-세션준비-워크플로우]]
>
> ### 인덱스와 메모리
> - [[260313-폴더-라우팅-인덱스]]
> - [[260313-파일명-코드-참조규칙]]
> - [[active-context]]
> - [[change-log]]
> - [[MEMORY|장기기억 (MEMORY.md)]]
> - [[260313-폴더체계-개편-결정]]
> - [[260316-워크스페이스OS-v2.1-도입]]
>
> ### 레거시 프로필
> - [[_system/agents/profiles/[클럽명]-ops|[클럽명]-ops]]
> - [[_system/agents/profiles/[클럽명]-thinker|[클럽명]-thinker]]
> - [[_system/agents/profiles/nugi-chronicle|nugi-chronicle]]
> - [[_system/agents/profiles/nugi-craft|nugi-craft]]
> - [[_system/agents/profiles/nugi-garden|nugi-garden]]
> - [[_system/agents/profiles/nugi-librarian|nugi-librarian]]
> - [[_system/agents/profiles/nugi-meta|nugi-meta]]
>
> ### 로그
> - [[260313-백업-병합-리포트]]
> - [[260313-루트-에이전트문서-통합로그]]
> - [[260313-README-및-에이전트규칙-반영로그]]

> [!info]- 팀 구조
> - `@session-team`: 리서치 -> 내용 구성 -> 자료 제작 -> 배포
> - `@research-team`: 병렬 다주제 리서치 -> 통합
> - `@studio-team`: 슬라이드 -> 공지 -> 발행
