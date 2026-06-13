---
title: MOC 프로젝트
status: live
created: 2026-03-15
updated: 2026-06-11
tags:
  - type/moc
  - project/moc
kind: moc
area: projects
up: "[[MOC-홈]]"
---

# MOC 프로젝트

> `projects.base`는 프로젝트 허브 파일 한 개를 한 행으로 읽는다.
> `PLAN`, `PROGRESS`, `docs`는 각 프로젝트 허브 안으로 들어가서 찾는다.
> 아래 **상태별 뷰** 임베드가 `project_status`로 동적 그룹화한다.

## 프로젝트 현황 (상태별)

> 단일 소스: 각 프로젝트의 상태는 PRJ 허브 frontmatter의 `project_status`가 정본이며, 아래 임베드(`projects.base`의 **상태별** 뷰)가 이를 동적으로 그룹화한다. 수동 상태 테이블 중복 관리는 폐기.

![[projects.base#상태별]]

> `PRJ-org-ops-workspace`(완료, 외부 리포 — 프레임워크 배포)는 `03_projects/` 내 허브 파일이 아니므로 위 임베드에 표시되지 않는다.

## 카탈로그

- [[projects.base]]
- [[260315-베이스-설계-매트릭스]]
