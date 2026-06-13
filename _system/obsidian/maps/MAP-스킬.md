---
title: MAP-스킬
kind: map
area: system
status: live
created: 2026-03-24
up: "[[MOC-시스템]]"
tags:
  - type/map
  - system/agents
---

# MAP-스킬

> 이 워크스페이스에서 사용하는 스킬의 사용자용 현황판.
> 실제 런타임은 `.agents/skills/`, `.claude/skills/`, `~/.codex/skills/`로 나뉘어 있지만,
> 사용자는 이 문서만 보면 현재 사용 가능한 스킬과 권장 흐름을 파악할 수 있다.
> 내부 구현 경로는 숨기고, 최신 스킬 기준으로만 수동 관리한다.

---

## 사용 가이드

- 이 문서는 **사용자용 단일 허브**다.
- 스킬 추가·수정 시 이 문서를 먼저 갱신한다.
- 런타임 경로 차이(`.agents/skills/`, `.claude/skills/`)는 구현 세부사항으로 취급한다.

## 자주 쓰는 스킬

| 스킬 | 호출 | 설명 | 상태 |
|------|------|------|------|
| obsidian:obsidian-cli | 자동/명시 | Obsidian vault 조작, 파일명 변경, 속성 수정 | active |
| obsidian:obsidian-bases | 자동/명시 | `.base` 생성·수정, Bases 뷰 설계 | active |
| discord-announce | `/discord-announce` | 공지 초안 작성 및 `01_ops/posts/` 저장 | active |
| release | `/release` | 릴리즈 초안 → 승인 → GitHub/Discord 배포 | active |
| superpowers:brainstorming | 자동/명시 | 구현 전 요구사항·설계 명확화 | active |
| superpowers:writing-plans | 자동/명시 | 다단계 구현 계획 작성 | active |
| superpowers:subagent-driven-development | 자동/명시 | 계획 기반 구현 + 단계별 리뷰 루프 | active |

## 추천 워크플로우

- Obsidian 구조 작업: `obsidian:obsidian-cli` → `obsidian:obsidian-bases`
- 공지/배포: `discord-announce` → `release`
- 큰 기능 설계: `superpowers:brainstorming` → `superpowers:writing-plans` → `superpowers:subagent-driven-development`

---

## 전체 스킬 카탈로그

### 세션 운영

| 스킬 | 호출 | 설명 | 상태 | 출처 |
|------|------|------|------|------|
| session-ops | `/session-ops [서브커맨드]` | 세션 수명주기 통합 진입점 — `setup`(폴더 생성) / `launch`(D-N 준비 점검) / `record`(기록 완성) / `guard`(에이전트 대화 세션 관리) | active | local |
| session-package | `/session-package [세션ID]` | 세션 자료 멤버 공유 폴더 패키징 — 공유 폴더 생성, 파일 복사, 네이밍 통일, README 작성 (세션 전·중·후 언제든) | active | local |
| session-transcript | `/session-transcript [세션ID]` | 녹음본 2-track 변환 — 정제 스크립트(오염 제거, 원문 보존) + 세션 정리(섹션별 요약) | active | local |
| session-close | `/session-close [세션ID]` | 세션 종료 마무리 — 폴더 파일 감사 + 허브·MOC-세션 링크 갱신 + 메모리 갱신 | active | local |
| session-setup | `/session-setup` | 세션 폴더 + 기획안 + 체크리스트 생성 (→ `session-ops setup`로 통합) | deprecated, 유예 2026-07-16 | local |
| session-launch | `/session-launch` | 세션 D-N일 기준 준비 상태 점검 (→ `session-ops launch`로 통합) | deprecated, 유예 2026-07-16 | local |
| session-record | `/session-record` | 세션 종료 후 기록 마무리 (→ `session-ops record`로 통합) | deprecated, 유예 2026-07-16 | local |
| session-guard | `/session-guard` | 에이전트 대화 세션 시작/종료 관리 (→ `session-ops guard`로 통합) | deprecated, 유예 2026-07-16 | local |
| weekly-review | `/weekly-review` | 볼트 전체 스캔 → 주간 현황 보고 | active | local |

### 볼트 건강

| 스킬 | 호출 | 설명 | 상태 | 출처 |
|------|------|------|------|------|
| vault-health | `/vault-health` | 네이밍·메타데이터·링크·깊이 위생 점검 | active | local |
| verify-implementation | `/verify-implementation` | 볼트 통합 검증 — vault-health → frontmatter-scan → tag-audit → link-audit 순차 실행 | active | local |
| link-audit | `/link-audit` | 고아 파일, 단절 군집, up 누락 진단 | active | local |
| frontmatter-scan | `/frontmatter-scan` | frontmatter 완성도 통계 | active | local |
| tag-audit | `/tag-audit` | 태그 일관성 — 미등록·위반 | active | local |
| tag-register | `/tag-register` | 새 태그 공식 등록 — tag-registry.md 업데이트 + decisions/ 기록 | active | local |
| manifest-check | `/manifest-check` | 볼트설정 매니페스트 ↔ .obsidian/ 비교 | active | local |
| clean-sweep | `/clean-sweep` | Icon·빈 폴더·.DS_Store·중복 탐지 | active | local |
| tool-governance | `/tool-governance` | `_system/tools` 운영 전반 점검 및 정리 | active | local |

> [!note] 결과 저장 경로 표준화 (2026-04-16)
> 위 진단 스킬(vault-health, link-audit, tag-audit, frontmatter-scan, manifest-check, verify-implementation) 실행 결과는 [[_system/obsidian/audits/README|진단 감사 저장 규약]]에 따라 `_system/obsidian/audits/`에 저장한다.

### 지식 관리

| 스킬               | 호출                  | 설명                              | 상태     | 출처    |
| ---------------- | ------------------- | ------------------------------- | ------ | ----- |
| reference-save   | `/reference-save`   | 조사 결과 → `_system/reference/` 저장 | active | local |
| reference-lookup | `/reference-lookup` | 저장된 레퍼런스 검색                     | active | local |
| library-classification | `/library-classification` | 도서관 분류 체계 기준으로 노트 선반·허브 배치 제안 | active | local |
| memory-update    | `/memory-update`    | 에이전트 메모리 갱신                     | active | local |
| update-governance | `/update-governance` | 변경사항 라우팅 — active-context / change-log / decisions / CLAUDE.md 판단 | active | local |
| week-promote     | `/week-promote`     | 주간 승격 — change-log·decisions에서 검증된 패턴을 MEMORY.md로 승격 | active | local |
| progress-update  | `/progress-update`  | PRJ-볼트v3 PROGRESS 갱신            | active | local |
| inbox-triage     | `/inbox-triage`     | `06_inbox/` 분류 및 라우팅 제안         | active | local |

### 콘텐츠 & 멤버

| 스킬               | 호출                  | 설명                                                      | 상태     | 출처    |
| ---------------- | ------------------- | ------------------------------------------------------- | ------ | ----- |
| slide-prep       | `/slide-prep`       | 슬라이드 MD → 외주 전달용 완성본                                    | active | local |
| member-onboard   | `/member-onboard`   | 새 멤버 운영 DB 파일 생성                                        | active | local |
| session-research | `/session-research` | 세션 발표 재료 리서치 — plan → 주제별 리서치 → `materials/` 저장         | active | local |
| session-review   | `/session-review`   | 세션 후 운영팀 리뷰 — 목표 달성 점검 → ops 리뷰 초안 → 다음 세션 힌트           | active | local |
| session-announce | `/session-announce` | 세션 공지 자동화 — 포스터 PNG 캡처 + `#announcements` 초안 발행 직전까지 준비 | active | local |
| discord-announce | `/discord-announce` | Discord 공지 초안 작성 — 채널별 포맷 자동 적용                         | active | local |
| drop-announce    | `/drop-announce`    | Drop 공지 자동화 — Excalidraw 카드 생성, 이미지 캡처, Drop 파일 생성      | active | local |
| drop-publish     | `/drop-publish`     | Drop 발행 자동화 — Discord + Notion 동시 발행                    | active | local |
| release          | `/release`          | 릴리즈 초안 → 승인 → GitHub 릴리즈 + Discord 공지                   | active | local |

### 시스템

| 스킬 | 호출 | 설명 | 상태 | 출처 |
|------|------|------|------|------|
| framework-sync | `/framework-sync` | [볼트명] → org-ops-workspace 동기화 | active | local |
| platform-setup | `/platform-setup` | Discord 봇·Notion API 초기 연결 — `.env` 생성 및 연결 테스트 | active | local |
| handoff | `/handoff` | 세션 간 컨텍스트 바톤 — 현재 세션을 일회용 핸드오프 문서로 압축해 `$TMPDIR`에 저장, 새 세션이 이어받음 (메모리OS와 별개의 휘발성 전달) | active | local |

### 외부 도구 연동

| 스킬 | 호출 | 설명 | 상태 | 출처 |
|------|------|------|------|------|
| nlm-skill | `/nlm-skill` | NotebookLM CLI + MCP 전문 가이드 | active | local |
| obsidian:obsidian-cli | 자동/명시 | Obsidian CLI 기반 vault 조작·플러그인 개발 | active | kepano/obsidian-skills |
| obsidian:obsidian-bases | 자동/명시 | Obsidian Bases (`.base`) 생성·편집 | active | kepano/obsidian-skills |
| obsidian:obsidian-markdown | 자동/명시 | Obsidian flavored markdown 작성·수정 | active | kepano/obsidian-skills |
| obsidian:json-canvas | 자동/명시 | JSON Canvas (`.canvas`) 생성·편집 | active | kepano/obsidian-skills |
| superpowers:brainstorming | 자동/명시 | 구현 전 요구사항·설계 명확화 | active | local mirror |
| superpowers:writing-plans | 자동/명시 | 다단계 구현 계획 작성 | active | local mirror |
| superpowers:subagent-driven-development | 자동/명시 | 계획 기반 구현 + 단계별 리뷰 루프 | active | local mirror |

---

## 도구 연결 스킬 뷰

### nlm

| 스킬 | 호출 | 설명 |
|------|------|------|
| nlm-skill | `/nlm-skill` | NotebookLM CLI + MCP 전문 가이드 |

### figma

| 스킬 | 호출 | 설명 |
|------|------|------|
| figma | 자동/명시 | Figma MCP 디자인 컨텍스트 조회, 자산·스크린샷·변수 확인 |
| figma-implement-design | 자동/명시 | Figma 노드를 실제 코드로 구현 |

### feynman

| 스킬 | 호출 | 설명 |
|------|------|------|
| 전용 스킬 없음 | — | 현재는 `_system/tools/feynman/260327-feynman-운영가이드.md`와 `@research`, `@research-team` 문맥에서 사용 |
| reference-lookup | `/reference-lookup` | feynman 평가/운영 근거 문서 검색 |

### github

| 스킬 | 호출 | 설명 |
|------|------|------|
| release | `/release` | 릴리즈 초안 → 승인 → GitHub 릴리즈 + Discord 공지 |
| gh-address-comments | 자동/명시 | GitHub PR 리뷰 코멘트 대응 |
| superpowers:writing-plans | 자동/명시 | 기능 작업을 Git/PR 단위 계획으로 정리 |

### kanban

| 스킬 | 호출 | 설명 |
|------|------|------|
| tool-governance | `/tool-governance` | `_system/tools` 정합성 점검 시 칸반 도구 운영 상태 함께 점검 |
| obsidian:obsidian-markdown | 자동/명시 | 칸반 보드 `.md` 구조 편집 |
| obsidian:obsidian-cli | 자동/명시 | 대상 노트 속성 확인·수정 |

---

## 관리 규칙

- 새 스킬 추가·수정 시 이 문서를 먼저 갱신한다.
- 사용자는 이 문서로 현황을 파악하고, 실제 런타임 경로는 숨긴다.
- 각 단일 도구 허브는 아래 `도구 연결 스킬 뷰`의 해당 섹션을 임베드해 보여준다.
