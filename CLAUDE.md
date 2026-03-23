# Org-Ops Workspace OS

> Local-first operations workspace for small organizations — clubs, student associations, startups, student teams
> Workspace OS v2.2 | Self-contained Claude Code configuration

---

## 처음 사용하는 경우

`/org-init`을 실행하세요 — 조직 정체성 파일 생성 + 폴더 구조 자동 설정.

---

## Quick Start

이 볼트에서 Claude Code는 에이전트 팀 + 개별 에이전트 모드로 작동한다.
에이전트 정의: `.claude/agents/`

### 에이전트 팀 (복합 목적 — 파이프라인 조율)

팀 오케스트레이터를 호출하면 내부에서 서브에이전트를 디스패치한다.
상세: `_system/agents/teams/README.md`

| 팀 | 에이전트 | 역할 | 파이프라인 |
|-----|---------|------|-----------|
| 세션준비팀 | `session-team` | 세션 전체 준비 조율 | 리서치 → 내용 구성 → 자료 제작 → 배포 |
| 리서치팀 | `research-team` | 독립 탐구 or 세션 피드 | 병렬 다주제 리서치 → 통합 |
| 스튜디오팀 | `studio-team` | 시각화·배포 전문 | 슬라이드 완성 → 공지 → 발행 |

### 조직 운영 에이전트 (단일 목적)

| 모드 | 에이전트 | 역할 | 주요 폴더 |
|------|---------|------|----------|
| 운영 | `ops` | 주간리뷰, 건강체크, 작업 조율 | `01_ops/` |
| 세션준비 | `prep` | 세션 기획안, 자료, 체크리스트 | `02_sessions/` |
| 기록 | `record` | 세션 기록, 결정 로그, 회고 | `02_sessions/`, `01_ops/meetings/` |
| 리서치 | `research` | 방법론 연구, 도구 분석, 사례 조사 | `05_library/research/` |
| 발행 | `publish` | 콘텐츠 제작, 공지, 플랫폼 발행 | `04_studio/`, `01_ops/posts/` |

### 볼트 구조 에이전트

| 모드 | 에이전트 | 역할 | 주요 Phase |
|------|---------|------|----------|
| 설계 | `architect` | 볼트 구조 총괄, Phase 판단, PROGRESS 관리 | 전체 |
| 그래프 | `graph-weaver` | 위키링크·up·related 연결, map 노트 생성, 고아 해소 | Phase 2 |
| 메타데이터 | `frontmatter-doctor` | frontmatter 진단·일괄 수정, 태그 정규화 | Phase 1 |

## Skills (반복 작업)

스킬 정의: `.claude/skills/`

### 조직 운영 스킬

| 스킬 | 설명 |
|------|------|
| `weekly-review` | 볼트 전체 스캔 → 현황 보고 생성 |
| `vault-health` | 네이밍·메타데이터·링크·깊이 위생 점검 |
| `session-setup` | 세션 폴더 + 기획안(plan) + 빈 기록(record) 자동 생성 |
| `slide-prep` | 슬라이드 MD → 디자인 스펙 + 이미지 가이드 + Gemini 프롬프트 삽입 (외주 전달 완성) |
| `session-launch` | D-N일 기준 세션 준비 상태 점검 + 당일 체크리스트 생성 |
| `session-record` | 세션 종료 후 record.md 완성 — 자료·피드백·회고 기록 |
| `member-onboard` | 새 멤버 운영 DB 파일 생성 (01_ops/members/) |
| `inbox-triage` | `06_inbox/` 파일 분류 및 라우팅 제안 |
| `memory-update` | 에이전트 메모리(active-context, change-log) 갱신 |

### 볼트 구조 스킬

| 스킬 | 설명 | 용도 |
|------|------|------|
| `link-audit` | 그래프 연결성 진단 — 고아 파일, 단절 군집, up 누락 | Phase 2 진단 |
| `tag-audit` | 태그 일관성 진단 — 미등록 태그, 네임스페이스 위반 | Phase 1 진단 |
| `frontmatter-scan` | frontmatter 완성도 통계 — 필드별·폴더별 채택률 | Phase 1 진단 |
| `manifest-check` | 볼트설정 매니페스트 ↔ .obsidian/ 비교 | Phase 3 검증 |
| `member-onboard` | 새 멤버 운영 DB 파일 생성 (01_ops/members/) | Phase 4 |
| `clean-sweep` | Icon, 빈 폴더, .DS_Store, 중복 탐지 및 삭제 제안 | Phase 0 |
| `progress-update` | 볼트 업그레이드 프로젝트 PROGRESS.md 갱신 | 매 세션 종료 시 |

---

## Workspace Rules (요약)

상세: `_system/rules.md`

### 폴더 구조

```
<볼트-루트>/
├── _system/        에이전트 설정, 규칙, 플랫폼 설정
├── 01_ops/         운영 (전략, 회의, 모집, 재정, 멤버, 템플릿)
├── 02_sessions/    세션 (기획, 자료, 기록)
├── 03_projects/    프로젝트 (PRJ- 접두사)
├── 04_studio/      브랜드 에셋, 디자인, 콘텐츠
├── 05_library/     리서치, 발행물, 시즌 아카이브
└── 06_inbox/       분류 전 임시 파일
```

### 파일 네이밍

- 기본: `YYMMDD-제목.md` (예: `260314-시즌1킥오프.md`)
- 세션: `_system/identity/workflow.md`의 `subfolder_pattern` 참조 (기본: `S{NN}-제목/`)
- 프로젝트: `PRJ-이름/`
- 템플릿: `tpl-이름.md`
- 깊이: 5레벨 최대, 3레벨 권장
- 상태: frontmatter `status:` 필드 (draft / live / archived). 파일명 접미사 금지.

### Frontmatter 필수

```yaml
---
title: 문서 제목
status: draft | live | archived
created: YYYY-MM-DD
---
```

선택: `updated`, `tags` (네임스페이스: ops/, session/, project/, research/, season/, type/)

### Cross-Mode Rules

1. 모든 .md 생성 시 frontmatter 포함
2. 해당 템플릿 있으면 반드시 사용 (`01_ops/templates/`)
3. 동일 자료 복사 금지 — `[[wikilink]]`로 연결
4. 한국어 기본, 기술 용어/코드는 영어 유지
5. 로컬에서 작성 → 외부에 요약 게시. 역방향 금지.

---

## Reference 저장소

에이전트와 스킬이 참고할 수 있는 조사·연구 결과 저장소.

| 경로 | 역할 |
|------|------|
| `_system/reference/README.md` | 인덱스 — 어떤 레퍼런스가 있는지 확인 |
| `_system/reference/claude-code/` | Claude Code 포맷·동작·API |
| `_system/reference/obsidian/` | Obsidian 플러그인·기능·문법 |
| `_system/reference/tools/` | 외부 도구 (Google Drive, Notion 등) |

| 스킬 | 설명 |
|------|------|
| `/reference-save [주제]` | 조사 결과 저장 + 인덱스 갱신 |
| `/reference-lookup [검색어]` | 저장된 레퍼런스 검색 및 반환 |

**에이전트 사용 규칙**: 외부 도구·포맷 관련 작업 전, Boot 시퀀스에서 `_system/reference/README.md`를 먼저 확인한다. 관련 레퍼런스가 있으면 해당 파일을 읽고 작업한다.

---

## Agent Knowledge Base

| 자료 | 경로 |
|------|------|
| 에이전트 아키텍처 | `_system/agents/에이전트아키텍처.md` |
| 에이전트 프로필 (7개) | `_system/agents/profiles/` |
| 운영 프로토콜 | `_system/agents/protocols/` |
| 폴더 라우팅 인덱스 | `_system/agents/indexes/폴더-라우팅-인덱스.md` |
| 파일명 참조 규칙 | `_system/agents/indexes/파일명-코드-참조규칙.md` |

에이전트 작업 전 관련 프로필과 라우팅 인덱스를 읽어 컨텍스트를 확보한다.

### 에이전트 간 위임

에이전트는 직접 호출하지 않고 **공유 메모리**를 통해 위임한다.
상세: `_system/agents/protocols/에이전트간-위임-프로토콜.md`

- `@ops` 주간리뷰에서 다른 에이전트 추천 작업을 active-context에 기록
- 다음 에이전트 호출 시 active-context를 읽어 작업 수행
- 파일 기반 핸드오프: 생산자의 산출물 → 소비자의 읽기 범위

## Agent Memory

```
_system/agents/memory/
├── active-context.md    ← 단일 파일, 항상 최신 상태로 덮어쓰기
├── change-log.md        ← 단일 파일, 날짜별 append
└── decisions/           ← 폴더, 개별 결정마다 1파일
```

| 파일 | 성격 | 관리 방식 |
|------|------|----------|
| `active-context.md` | 조직 현재 상태 스냅샷 | **덮어쓰기** — 항상 최신만 유지 |
| `change-log.md` | 운영 변경 이력 | **append** — 날짜별 섹션 추가 |
| `decisions/*.md` | 구조적 의사결정 기록 | **개별 파일** — 각 결정은 독립적 |

**작업 시작 전**: `active-context.md`를 읽어 현재 상태를 파악한다.
**작업 완료 후**: 변경사항이 있으면 `change-log.md`에 날짜 섹션으로 append한다.
**역사적 마일스톤**: 조직 연혁에 남길 만한 사건은 `01_ops/strategy/조직타임라인.md`에도 기록한다.

## Templates

위치: `01_ops/templates/`

| 템플릿 | 용도 |
|--------|------|
| `tpl-주간리뷰.md` | 주간 현황 보고 |
| `tpl-세션기획.md` | 세션 기획안 |
| `tpl-세션기록.md` | 세션 후 기록 |
| `tpl-회의록.md` | 운영 회의록 |
| `tpl-연구노트.md` | 주제 탐구 정리 |
| `tpl-결정로그.md` | 의사결정 기록 |
| `tpl-멤버프로필.md` | 멤버 개인 공간 README |

## Identity

`_system/identity/` 폴더 참조.

- **`_system/identity/identity.md`** — 조직명, 유형, 성격, 태그라인 (조직에 맞게 설정)
- **`_system/identity/brand.md`** — 브랜드 톤, 디자인 가이드라인
- **`_system/identity/workflow.md`** — 세션 명명 규칙(`subfolder_pattern`), 운영 주기 등

> 처음 설정하려면 `/org-init`을 실행하세요.

## Platform Roles

| 툴 | 역할 |
|----|------|
| 로컬 (Obsidian) | SoT — 기획, 집필, 원본 보관 |
| Notion | 협업, 공유 대시보드 |
| Google Drive | 대용량 파일 (영상, PDF, PPT) |
| Discord | 실시간 소통, 공지 |

**원칙**: 로컬에서 작성 → 외부에 요약·링크 게시. 역방향 금지.

상세: `_system/identity/platform.md` (플랫폼 역할 정의)
다인 운영: `_system/agents/protocols/다인운영-협업규칙.md`

---

## Obsidian 읽기 레이어

볼트를 3층으로 읽는다: **MOC** (허브) + **Bases** (속성 뷰) + **Dataview** (감사/계산)

| 자료 | 경로 |
|------|------|
| MOC 홈 (진입점) | `_system/obsidian/mocs/MOC-홈.md` |
| MOC 전체 | `_system/obsidian/mocs/` |
| Base 설정 | `_system/obsidian/bases/` |
| 대시보드 | `_system/obsidian/dashboards/` |
| Dataview 쿼리 | `_system/obsidian/Dataview-쿼리-모음.md` |
| 프론트매터 계약 | `_system/obsidian/프론트매터-계약.md` |
| 업그레이드 설계 | `_system/obsidian/옵시디언-업그레이드-설계.md` |

### Frontmatter 확장 (폴더별 권장)

기본 3필드(title, status, created) 외에 `kind`와 `area`를 추가하면 Bases와 Dataview가 더 정확하게 작동한다.

```yaml
kind: meeting | session | project | research | inbox | moc
area: ops | sessions | projects | library | studio | inbox | system
```

상세: `_system/obsidian/프론트매터-계약.md`
