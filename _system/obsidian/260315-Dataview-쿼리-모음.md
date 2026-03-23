---
title: Dataview 쿼리 모음
kind: system
area: system
status: live
created: 2026-03-15
updated: 2026-03-15
tags:
  - system/obsidian
  - system/dataview
up: "[[MOC-시스템]]"
---

# Dataview 쿼리 모음

## frontmatter 누락 점검

```dataview
TABLE file.folder AS folder, status, created, updated
FROM ""
WHERE !status OR !created
SORT file.path ASC
```

## 최근 수정 문서

```dataview
TABLE status, updated, file.folder
FROM ""
WHERE updated
SORT updated DESC
LIMIT 20
```

## draft 방치 문서

```dataview
TABLE created, updated, file.folder
FROM ""
WHERE status = "draft"
SORT coalesce(updated, created) ASC
```

## 세션 관련 문서

```dataview
TABLE status, created, updated
FROM "02_sessions"
WHERE contains(file.path, "plan") OR contains(file.path, "record")
SORT file.name ASC
```

## 미완료 task

```dataview
TASK
FROM "01_ops" OR "02_sessions" OR "03_projects"
WHERE !completed
SORT file.name ASC
```
