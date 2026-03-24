---
title: MAP-플랫폼
kind: map
area: system
status: live
created: 2026-03-24
up: "[[MOC-시스템]]"
tags:
  - type/map
  - system/obsidian
---

# MAP-플랫폼

> 이 조직이 사용하는 외부 플랫폼 설정·연동 문서의 중간 허브.
> 플랫폼 역할 정의: [[_system/identity/platform|identity/platform.md]]

---

## 플랫폼 목록

> 사용 중인 플랫폼에 따라 아래를 채운다.
> 플랫폼별 상세 설정이 생기면 `_system/platform/{플랫폼명}/` 폴더에 추가한다.

| 플랫폼 | 역할 | 상세 문서 |
|--------|------|-----------|
| (소통 플랫폼) | 실시간 소통, 공지 | — |
| (협업 플랫폼) | 공유 대시보드, 파일 | — |

---

## 자동 목록 (Dataview)

```dataview
TABLE title, status
FROM "_system/platform"
WHERE file.name != "MAP-플랫폼" AND file.name != "README" AND kind != "map"
SORT file.folder ASC, file.name ASC
```
