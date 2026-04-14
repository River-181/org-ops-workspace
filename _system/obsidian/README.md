---

title: Obsidian 운영 설정
status: live
created: 2026-03-16
tags:
  - system/obsidian
up: "[[MOC-시스템]]"
---

# Obsidian 운영 설정

이 폴더는 [볼트명] 볼트의 Obsidian 읽기 레이어를 정의한다.

## 구조

```
obsidian/
├── design/                              설계·스펙 문서
│   ├── 260315-옵시디언-업그레이드-설계.md  전체 설계 문서
│   ├── 260315-프론트매터-계약.md          속성 스키마
│   ├── 260315-베이스-설계-매트릭스.md      Base 우선순위
│   ├── 260315-Dataview-쿼리-모음.md       재사용 쿼리
│   ├── 260318-태그-레지스트리.md           태그 네임스페이스 정의
│   ├── 260320-볼트설정-매니페스트.md        .obsidian/ 설정 명세
│   ├── 260326-칸반시스템-설계.md           칸반 시스템 설계
│   └── 260326-칸반시스템-구현계획.md        칸반 구현 계획
├── tag-registry.md                      공식 태그 레지스트리 (tag-register 스킬이 관리)
├── maps/                                MAP 허브 (8개)
│   ├── MAP-에이전트.md  ├── MAP-플랫폼.md  ├── MAP-도구.md
│   ├── MAP-Discord.md  ├── MAP-스킬.md
│   ├── MAP-드롭.md     ├── MAP-멤버.md
│   └── MAP-라이브러리.md
├── mocs/                                MOC 허브 (6개)
│   ├── MOC-홈.md                        최상위 진입점
│   ├── MOC-운영.md    ├── MOC-세션.md
│   ├── MOC-프로젝트.md ├── MOC-스튜디오.md
│   └── MOC-라이브러리.md
├── audits/                              Dataview 감사 노트
│   ├── 라이브러리-감사.md
│   └── 볼트-감사.md
├── dashboards/                          Dataview 대시보드
│   ├── 운영-대시보드.md
│   └── 지식-대시보드.md
├── bases/                               Obsidian Base 설정
│   ├── projects.base  ├── sessions.base  ├── library.base
│   ├── tools.base     ├── inbox.base
│   └── supporting/                      보조 Base 모음
│       ├── research.base   ├── references.base
│       ├── articles.base   ├── posts.base
│       ├── drops.base      ├── members.base
│       ├── ops-meetings.base
│       ├── platforms.base  ├── discord.base
│       └── agents.base
└── kanban/                              칸반 보드 (3개)
    ├── 칸반-메인.md  ├── 칸반-드롭.md  └── 칸반-세션.md
```

## 읽기 계층

1. **MOC**: 사람이 길을 잃지 않게 하는 허브. 위키링크로 연결.
2. **MAP**: 큰 주제의 동적 진입점. Base와 감사 노트로 분기.
3. **Bases**: 폴더 안 문서를 속성(frontmatter) 기준으로 읽는 카탈로그.
   루트 `bases/`에는 핵심 Base만 두고, 세부 운영용은 `bases/supporting/`로 내린다.
   루트 Base는 가능한 한 `대표 허브 파일 1개 = 1행` 원칙을 따른다.
   예외는 `library.base`로, 허브와 발행물을 함께 본다.
4. **Audit / Dataview**: 계산, 누락 점검, 최근 변화 추적. 감사용.

## 필요 플러그인

- **Core**: Properties, Backlinks, Bookmarks, Outline, Bases
- **Community**: Dataview (우선)
- **선택**: Tasks, Templater

## 출처

`05_library/seasons/2026-S1/[볼트명]-Obsidian-Lab/_system/obsidian_lab/`에서 승격.
