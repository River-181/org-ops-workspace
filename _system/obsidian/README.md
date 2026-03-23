---
title: Obsidian 운영 설정
status: live
created: 2026-03-16
tags:
  - system/obsidian
up: "[[MOC-시스템]]"
---

# Obsidian 운영 설정

이 폴더는 볼트의 Obsidian 읽기 레이어를 정의한다.

## 구조

```
obsidian/
├── 260315-옵시디언-업그레이드-설계.md    전체 설계 문서
├── 260315-프론트매터-계약.md            속성 스키마
├── 260315-베이스-설계-매트릭스.md        Base 우선순위
├── 260315-Dataview-쿼리-모음.md         재사용 쿼리
├── 260320-볼트설정-매니페스트.md         실제 Obsidian 설정 요약
├── tag-registry.md                     태그 레지스트리
├── mocs/                                허브 노트
│   ├── MOC-홈.md                        최상위 진입점
│   ├── MOC-운영.md
│   ├── MOC-세션.md
│   ├── MOC-프로젝트.md
│   ├── MOC-스튜디오.md
│   └── MOC-라이브러리.md
├── dashboards/                          Dataview 대시보드
│   ├── 260315-운영-대시보드.md
│   └── 260315-지식-대시보드.md
└── bases/                               Obsidian Base 설정
    ├── ops-meetings.base
    ├── sessions.base
    ├── projects.base
    ├── research.base
    └── inbox.base
```

## 3층 읽기

1. **MOC**: 사람이 길을 잃지 않게 하는 허브. 위키링크로 연결.
2. **Bases**: 폴더 안 문서를 속성(frontmatter) 기준으로 테이블 뷰.
3. **Dataview**: 계산, 누락 점검, 최근 변화 추적. 감사용.

## 필요 플러그인

- **Core**: Properties, Backlinks, Bookmarks, Outline, Bases
- **Community**: Dataview (우선)
- **선택**: Tasks, Templater
