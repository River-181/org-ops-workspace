---
title: org-ops-workspace 변경 이력
kind: log
status: live
created: 2026-03-24
---

# CHANGELOG — org-ops-workspace

> NUGI-Core에서 범용 프레임워크 업데이트가 있을 때마다 이 파일에 기록한다.
> 각 항목은 날짜 + 변경 범위 + 마이그레이션 노트를 포함한다.
> Source: `/framework-sync` 스킬 실행 시 자동 참조.

---

## 2026-04-17 — v1.6.0: session-ops 통합 스킬 + 온보딩 가이드 3종 + RUNBOOK

### 변경 내용

**session-ops 통합 스킬 (.claude/skills/session-ops/)**
- `setup` / `launch` / `record` / `guard` 4개 서브커맨드를 단일 스킬로 통합
- 기존 session-setup / session-launch / session-record / session-guard는 deprecated (2026-07-16 삭제 예정)
- guard 서브커맨드: 에이전트 대화 세션 격리 + AAR 3줄 패턴

**온보딩 가이드 3종 신규 (01_ops/guides/)**
- `온보딩-Lv1-Day1.md` — 전체 생태계 지도, 폴더 구조, Source of Truth 개념
- `온보딩-Lv2-Week1.md` — 주간 루틴 흐름도, 각 스킬의 존재 이유, FAQ
- `온보딩-Lv3-Month1.md` — 세션 수명주기 완주, 패턴 승격 판단 기준

**RUNBOOK-볼트유지.md 신규 (01_ops/guides/)**
- 주 1회 30분 체크리스트 (주간 루틴 + 세션 준비)
- 월 1회 건강성 진단 절차
- 세션 실행 주기 표 (session-ops 서브커맨드 기준)
- 장애 대응, 인수인계 규칙

**메모리 스킬 분업 경계 정제**
- `memory-update`: routine 세션 기록 전용
- `update-governance`: 라우팅 판단 필요 시 (특수)
- `week-promote`: L1/L2 → L3 장기기억 승격

**NUGI 잔존 표현 제거**
- SETUP-GUIDE.md 예시값, MAP-에이전트.md 레거시 프로필, MAP-드롭.md, MOC-스튜디오.md, 칸반 파일 6건

### 마이그레이션 노트

- `/session-ops setup` → 기존 `/session-setup` 대체
- `/session-ops launch` → 기존 `/session-launch` 대체
- `/session-ops record` → 기존 `/session-record` 대체
- `/session-ops guard` → 기존 `/session-guard` 대체
- RUNBOOK 섹션 2의 체크리스트가 주간 루틴 표준 참조점

---

## 2026-04-16 — v1.5.0: 진단 감사 저장 규약 + Drop 발행 표준 + 훅 정책

### 변경 내용

**진단 감사 저장 규약 (_system/obsidian/audits/)**
- `_system/obsidian/audits/README.md` 신규: 파일 네이밍·frontmatter·섹션 순서·실행 주기 표준화
- 6개 진단 스킬 출력 경로 `01_ops/reviews/` → `_system/obsidian/audits/`로 통일
  - `vault-health`, `verify-implementation`, `frontmatter-scan`, `link-audit`, `manifest-check`, `tag-audit`
- frontmatter 표준: `kind: audit`, `source_skill:`, `up: "[[MOC-시스템]]"` 필드 추가

**Drop 발행 표준 (drop-announce/SKILL.md)**
- Drop 파일 속성 목록 갱신: `tags`, `source`, `link`, `memo`, `created`, `published_at`, `up`, `discord_message_id`, `notion_page_id` 추가; 비표준 필드 추가 금지 명시
- `공유 내용 섹션 형식` 신규: 500자 내외, Discord 형식, `[ Drop | #NNNN ]` footer 규격

**운영 표준 정비**
- `_system/rules.md`: `## 6. 자동화 훅 정책` 섹션 추가 (settings.json 관리 + 신규 훅 등록 절차)
- `tpl-결정로그.md`: `status: live`, `tags: ops/decision` 표준화
- `MAP-에이전트.md`: 슬림화 원칙 가이드 추가 (active-context 핵심 상태만 유지)
- `MAP-드롭.md`: drop-0019~0021 그래프 링크 추가

### 마이그레이션 노트

기존 조직에서 진단 스킬을 이미 사용하고 있다면:
- `01_ops/reviews/` 아래 기존 진단 보고서를 `_system/obsidian/audits/`로 이동
- frontmatter에 `kind: audit`, `source_skill:`, `up: "[[MOC-시스템]]"` 추가
- `_system/obsidian/audits/README.md`의 섹션 순서 규약(6개 섹션 고정)에 맞게 정리

---

## 2026-04-14 — v1.4.0: 장기기억 시스템 + 신규 스킬 7종 + Obsidian 인프라 확장

### 변경 내용

**신규 스킬 7종**
- `session-guard` — 세션 시작·종료 프로토콜
- `update-governance` — 변경사항 라우팅 판단
- `verify-implementation` — 볼트 통합 검증 (4개 스킬 순차 실행)
- `tag-register` — 태그 공식 등록 + decisions/ 기록
- `week-promote` — 검증된 패턴을 MEMORY.md로 승격
- `drop-announce` — Drop 공지 자동화 (Excalidraw + Discord + Notion)
- `release` — 릴리즈 자동화 (git push + GitHub release + Discord 공지)

**장기기억 시스템**
- `_system/agents/memory/MEMORY.md` — 장기기억 1차 인터페이스 (스텁)
- `_system/agents/memory/long-term/` — 운영 패턴·조직 프로필·시즌 회고 구조 (스텁)

**에이전트 업데이트**
- `ops`, `research`, `research-team`, `studio-team` 에이전트 개선
- `memory-update` 스킬 대폭 재작성

**Obsidian 인프라 확장**
- `_system/obsidian/bases/` — Base 파일 추가
- `_system/obsidian/design/` — 설계 문서 추가
- `_system/obsidian/kanban/` — 칸반 보드 추가
- `_system/obsidian/dashboards/` — 대시보드 추가
- `_system/obsidian/maps/`, `mocs/`, `audits/` 갱신

**템플릿 + 기타**
- `01_ops/templates/tpl-drop.md` 추가
- `tag-registry.md` 네임스페이스 구조 초기화
- `_system/rules.md` 업데이트

### 마이그레이션 노트

- 신규 스킬은 `.claude/skills/{name}/SKILL.md` 폴더 구조로 추가됨
- 장기기억은 빈 스텁 제공 — 운영 후 `/memory-update`로 채울 것
- `tag-registry.md`는 네임스페이스만 정의 — `/tag-register`로 태그 추가

---

## 2026-03-26 — v1.3.0: 플랫폼 자동화 스킬 — Discord + Notion 발행 워크플로우

### 변경 내용

**신규 스킬 3개**
- `/platform-setup` — Discord 봇 + Notion API 초기 연결을 AI가 단계별로 안내. `.env` 자동 생성 + 연결 테스트 포함
- `/drop-publish` — `drop_status: ready` 파일을 Discord #share + Notion Drops DB에 동시 발행. `drops.csv` 자동 갱신
- `/discord-announce` — 채널 유형(ops/announce/showcase)별 Discord 공지 초안 생성

**환경변수 템플릿**
- `_system/tools/.env.template` 신설 — 플랫폼 연결에 필요한 모든 환경변수 문서화
- `DISCORD_USER_{이름}` 패턴 — 운영자 멘션 매핑 (하드코딩 없이 `.env`에서 동적 로드)

**`/org-init` 완료 메시지 업데이트**
- 초기화 완료 후 `/platform-setup` 안내 추가

### 마이그레이션 노트

신규 기능 — 기존 설정에 영향 없음.

Discord + Notion 자동화를 쓰려면:
1. `/platform-setup` 실행 (AI가 단계별 안내)
2. 이후 `/drop-publish` 사용 가능

---

## 2026-03-24 — v1.2.1: 에이전트·스킬 내용 업데이트

### 변경 내용

- 에이전트 12개 최신화 (ops, prep, record, research, publish, ot-session, session-team, research-team, studio-team, architect, graph-weaver, frontmatter-doctor)
- 스킬 13개 최신화 (session-setup, weekly-review, vault-health, session-launch, session-record, member-onboard, memory-update, slide-prep, link-audit, tag-audit, frontmatter-scan, clean-sweep, progress-update)

### 마이그레이션 노트

없음 (내용 업데이트만, 구조 변경 없음)

---

## 2026-03-24 — v1.2.0: 스킬 폴더 구조 + maps/ + 프론트매터 v3

### 변경 내용

**스킬 폴더 구조 통일**
- `.claude/skills/{name}.md` → `.claude/skills/{name}/SKILL.md`
- frontmatter에 `name:` 필드 추가 (Claude Code 스킬 이름 명시)
- 스킬 인덱스 파일 신설: `.claude/skills/MAP-스킬.md`

**`_system/obsidian/maps/` 폴더 신설**
- MOC와 평행하는 MAP 전용 폴더
- MAP-에이전트.md, MAP-플랫폼.md, MAP-도구.md 기본 파일 포함
- MAP = Dataview 자동집계 + 수동 섹션 혼용

**MOC/MAP/INDEX 개념 확립**
- MOC: 수동 큐레이션 도메인 허브 (`_system/obsidian/mocs/`)
- MAP: Dataview 자동집계 주제 클러스터 (`_system/obsidian/maps/`)
- INDEX: Dataview 전용 자동 목록 (데이터 폴더 내부)
- 프론트매터 계약 v3에 반영: `index` kind, `registry` kind 추가

**`_system/tools/` 패턴 문서화**
- `_system/tools/README.md` 신설 — 도구 등록·관리 레지스트리
- 도구 폴더 표준 구조 정의
- 에이전트 boot 시퀀스 (도구 사용 전 운영가이드 읽기)

### 마이그레이션 노트

기존 스킬 파일을 사용 중이라면:
1. `.claude/skills/*.md` → `.claude/skills/{name}/SKILL.md`로 이동
2. frontmatter 첫 줄에 `name: {skill-name}` 추가

---

## 2026-03-23 — v1.1.0: identity 6파일 구조 + 범용 프레임워크 초기 배포

### 변경 내용

- `_system/identity/` 6파일 구조 확립 (identity, soul, culture, workflow, platform, brand)
- `/org-init` 스킬 — 7가지 org_type 지원 온보딩
- CLAUDE.md + 에이전트 12개 + 스킬 17개 범용화
- GitHub Template 공개: https://github.com/River-181/org-ops-workspace

---

## 버전 규칙

- `v{MAJOR}.{MINOR}.{PATCH}`
- MAJOR: 폴더 구조·에이전트 아키텍처 대규모 변경
- MINOR: 새 스킬·에이전트 추가, 새 패턴 도입
- PATCH: 버그 수정, 문서 개선, 마이그레이션 가이드
