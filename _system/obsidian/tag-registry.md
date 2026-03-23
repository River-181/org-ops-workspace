---
title: 태그 레지스트리
kind: system
area: system
status: live
created: 2026-03-18
updated: 2026-03-18
up: "[[MOC-시스템]]"
tags:
  - system/schema
---

# 태그 레지스트리

> 이 볼트에서 사용 가능한 모든 태그의 공식 목록.
> **새 태그를 사용하기 전에 여기 등록한다.** 미등록 태그는 `tag-audit` 스킬이 경고한다.
>
> 이 파일에 `#tag` 형식으로 태그가 직접 작성되어 있으므로,
> 파일을 열면 Obsidian Tags 패널에 자동 집계된다.

---

## 네임스페이스 구조

| 네임스페이스 | 의미 | 주 사용처 |
|---|---|---|
| `ops/` | 조직 운영 | `01_ops/` |
| `session/` | 세션 기획·기록 | `02_sessions/` |
| `project/` | 프로젝트 식별자 | `03_projects/` |
| `research/` | 리서치·방법론 주제 | `05_library/research/` |
| `library/` | 도서·자료 분류 (LYT 기반) | `05_library/` |
| `platform/` | 외부 플랫폼 종속 | `_system/platform/` |
| `role/` | 멤버 역할 구분 | `05_library/members/` |
| `season/` | 시즌 구분 | 세션·기록 전반 |
| `type/` | 콘텐츠 형식 | 전체 |
| `system/` | 볼트 시스템·설정 | `_system/` |

---

## ops/ — 조직 운영

| 태그 | 설명 |
|---|---|
| #ops/timeline | 타임라인·마일스톤 |
| #ops/milestone | 주요 달성 지점 |
| #ops/history | 조직 역사 기록 |
| #ops/strategy | 전략·방향 문서 |
| #ops/meeting | 운영 회의록 |
| #ops/recruit | 모집·선발 |
| #ops/finance | 재정·예산 |
| #ops/member | 멤버 관리 |
| #ops/review | 주간·분기 리뷰 |
| #ops/decision | 의사결정 로그 |
| #ops/post | 공지·발행물 |

## session/ — 세션

| 태그 | 설명 |
|---|---|
| #session/plan | 세션 기획안 |
| #session/record | 세션 기록 |
| #session/material | 세션 자료 |
| #session/checklist | 체크리스트 |
| #session/review | 세션 회고 |

## project/ — 프로젝트

> 새 프로젝트 생성 시 `#project/PRJ-이름` 형식으로 이 표에 추가한다.

| 태그 | 설명 |
|---|---|
| #project/PRJ-예시 | 예시 프로젝트 (실제 프로젝트로 교체) |

## research/ — 리서치 주제

| 태그 | 설명 |
|---|---|
| #research/pkm | PKM·지식 관리 방법론 |
| #research/learning | 학습 방법론 |
| #research/community | 커뮤니티·모임 운영 |
| #research/tool | 도구·플랫폼 분석 |
| #research/design | 디자인·브랜드 |

## library/ — 도서·자료 분류 (LYT 기반)

| 태그 | 분류 |
|---|---|
| #library/000 | 지식 관리 (PKM, 메타) |
| #library/100 | 자기 관리 (습관, 생산성, 목표) |
| #library/200 | 철학·심리학·종교 |
| #library/300 | 사회과학 (커뮤니티, 교육) |
| #library/400 | 언어·커뮤니케이션·수사학 |
| #library/500 | 자연과학 |
| #library/600 | 응용과학 (기술, AI, 엔지니어링) |
| #library/700 | 예술·레크리에이션·디자인 |
| #library/800 | 문학·글쓰기 |
| #library/900 | 역사·전기·지리 |

## platform/ — 외부 플랫폼

| 태그 | 설명 |
|---|---|
| #platform/obsidian | Obsidian 설정·플러그인 |
| #platform/notion | Notion 연동·설정 |
| #platform/google | Google Drive·Docs·Forms |

## season/ — 시즌

> 조직에 맞게 시즌 태그를 추가한다.

| 태그 | 설명 |
|---|---|
| #season/pre | 시즌 시작 전 |

## type/ — 콘텐츠 형식

| 태그 | 설명 |
|---|---|
| #type/template | 템플릿 파일 |
| #type/map | map/index 노드 — 중간 주제 클러스터 |
| #type/moc | MOC — 도메인 허브 |
| #type/log | 로그·이력 |
| #type/guide | 가이드·튜토리얼 |
| #type/announcement | 공지·발표 |

## role/ — 멤버 역할

> 조직에 맞게 역할 태그를 설정한다.

| 태그 | 설명 |
|---|---|
| #role/admin | 관리자 역할 (조직에 맞게 수정) |
| #role/member | 일반 멤버 역할 |

## system/ — 볼트 시스템

| 태그 | 설명 |
|---|---|
| #system/obsidian | Obsidian 설정·플러그인 |
| #system/schema | frontmatter·네이밍 규칙 |
| #system/agent | 에이전트·스킬 관련 |
| #system/reference | 참고 자료 저장소 |
| #system/identity | 조직 정체성 파일 |

---

## 운영 규칙

1. 새 태그 사용 전 이 파일에 등록 → 표에 행 추가
2. 네임스페이스 없는 단독 태그 사용 금지 (`#pkm` → `#research/pkm`)
3. 프로젝트 태그는 `#project/PRJ-이름` 형식 고정
4. `tag-audit` 스킬 실행 시 볼트 전체 태그 ↔ 이 파일 비교
