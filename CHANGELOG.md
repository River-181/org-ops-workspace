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
