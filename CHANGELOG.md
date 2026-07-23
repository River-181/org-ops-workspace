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

## 2026-07-24 — v1.12.0: Drop 발행 스킬 개선 + link-audit NFC 버그픽스

### 변경 내용

**수정 스킬 (3종)**
- `drop-announce/SKILL.md`: 이미지 경로 규약(`04_studio/assets/etc/` 전체경로) 표준화, "카카오톡 공유용" 섹션 대폭 확장(분량 유동화·아크형 드롭 산문 규칙), Insight 유형 지원, `related:` 필드 문서화
- `drop-publish/SKILL.md`: 이미지 임베드 두 형식(전체경로/바레 파일명) 지원 설명, 파일 미존재 시 무음 skip 동작 문서화
- `link-audit/SKILL.md`: NFC 정규화 필수 처리 추가(macOS 한글 파일명 NFD 오탐 방지), 코드펜스·인라인코드 내 `[[ ]]` 제외, `up: "[[MAP-시스템]]"` 필수 필드

**수정 템플릿 (1종)**
- `01_ops/templates/tpl-drop.md`: 섹션 순서 재구성(이미지→공유내용→카카오톡공유용→링크), 인라인 가이드 주석 추가, 카카오톡 공유용을 코드블럭 필수로 명시

**수정 레퍼런스 (1종)**
- `_system/reference/claude-code/260609-oh-my-claudecode-간단설명서.md`: `up:` 링크 추가

### 마이그레이션 노트

- 기존 드롭 파일이 바레 파일명(`![[파일명.png]]`) 이미지 임베드를 쓰고 있다면 그대로 유지 가능 — `drop-publish`가 두 형식 모두 지원.
- 신규 드롭부터는 `04_studio/assets/etc/` + 전체경로 임베드 규약을 적용할 것.

---

## 2026-07-24 — v1.11.0: content-skills 영문 글쓰기 5종 + git 접근통제 아키텍처

### 변경 내용

**신규 문서 (1종)**
- `_system/obsidian/design/260711-git운영-접근통제-아키텍처.md`: git·GitHub 접근통제 설계 정본 — SoT 원칙, Syncthing→맥→GitHub push 흐름, 브랜치 보호, 보안 근거, PII 잔여 리스크, 커밋 정책, 에이전트 수칙 포함

**수정 시스템 문서 (2종)**
- `_system/agents/indexes/스킬-카탈로그.md`: 영문 글쓰기 content-skills 섹션 신설 (dumbify·storytelling·viral-hooks·anti-ai-writing·voice-dna)
- `_system/tools/README.md`: im-not-ai 설치방식 마켓플레이스 플러그인으로 갱신 + content-skills 도구 행 추가

**수정 맵 (1종)**
- `_system/obsidian/mocs/MOC-시스템.md`: git 접근통제 아키텍처 문서 링크 추가

### 마이그레이션 노트

- content-skills 도입 시: `_system/tools/content-skills/` 폴더 생성 후 외부 `artemnovitckii/content-skills`를 배치, SKILL.md 있는 4종(`dumbify`·`storytelling`·`viral-hooks`·`anti-ai-writing`)을 `~/.claude/skills/`에 심링크. `voice-dna`는 가이드 폴더로만 보관.
- git 아키텍처 문서의 `[운영자계정]`, `[협업자계정]` 플레이스홀더를 실제 GitHub 계정명으로 교체.
- Section 8 조치 이력 테이블에 실제 적용 날짜와 내용을 기록.

---

## 2026-07-11 — v1.10.0: AX 사령탑 프로토콜 + 스킬 카탈로그 + 태그 레지스트리 + Google Calendar

### 변경 내용

**신규 운영 문서 (3종)**
- `_system/agents/protocols/260621-AX-사령탑-운영-프로토콜.md`: AX 전환 핵심 운영 프로토콜 — AI 사령탑(LLM 에이전트, 24/7) 지휘 계층·역할 경계·승인 게이트·SoT 무결성 원칙·자가증강 스킬 게이트·OPS-ROOM 채널 프리미티브 정의
- `_system/agents/indexes/스킬-카탈로그.md`: 스킬 카탈로그 인덱스 신규 분리 (CLAUDE.md 인라인 → 독립 파일)
- `_system/obsidian/maps/MAP-시스템.md`: `_system/` 인프라 전용 맵 노트 신규 생성 (AX 전환 섹션 포함)

**신규 도구 (1종)**
- `_system/tools/google-calendar/google-calendar.md`: Google Calendar 도구 허브 — 회원 공유 캘린더·운영팀 캘린더 역할 분리, OAuth 연결 전 `.ics` handoff 운영 패턴, AI 사령탑 작업 방식

**수정 스킬 (2종)**
- `tag-audit/SKILL.md`: 사이드카 파일 처리 추가, Python 감사 스크립트 구조 개선, 섹션 재편
- `vault-health/SKILL.md`: Section 10 — 일반 .md / 사이드카 .md 분리 처리; Section 11 — `up: "[[MAP-시스템]]"` 필수 필드 추가

**수정 시스템 문서 (3종)**
- `_system/rules.md`: Section 6 AX 운영 규칙 신규 삽입 (AI 사령탑 위상·볼트 읽기전용 원칙·자가증강 게이트·OPS-ROOM 프리미티브·멤버 응답 경계)
- `_system/obsidian/maps/MAP-도구.md`: `google-calendar` 추가
- `_system/obsidian/tag-registry.md`: 전체 태그 섹션 완성 (ops/·session/·type/·research/·system/ 등), 클럽별 고유 태그는 플레이스홀더 패턴으로 대체
- `_system/tools/README.md`: `google-calendar` 행 추가

### 마이그레이션 노트

- **AX 프로토콜**: `[[260621-AX-사령탑-운영-프로토콜]]` 도입 전에는 `_system/rules.md` Section 6가 비활성이다. AX 전환 실행 시 `(도입 시 활성화)` 주석을 제거하고 프로토콜 전체를 검토한다.
- **OPS-ROOM 채널**: `#ops-command`, `#ops-hermes`, `#ops-system` 채널명은 Discord 운영 구조에 맞춰 클럽별로 조정한다.
- **Google Calendar**: OAuth 연결 전에는 `01_ops/calendar/`에 `.ics` 파일을 생성해 수동 import로 운영한다.
- **태그 레지스트리**: `season/[YYYY-SN]` 패턴을 실제 시즌 태그로 교체한다 (예: `season/2026-S1`).

---

## 2026-06-20 — v1.9.2: MEMORY.md 구조 스텁 + 도구 레지스트리 갱신

### 변경 내용

**메모리 시스템**
- `_system/agents/memory/MEMORY.md`: 빈 스텁 → 장기기억 구조 템플릿으로 교체. 클럽 프로필·운영 패턴·교훈·도구 선택 근거·시즌 기록 섹션이 포함된 채우기 가능한 스텁.

**도구 레지스트리 (`_system/tools/README.md`)**
- `skillspector` 행 신규 추가 — 외부 AI 스킬 보안 스캐너 (정적 분석 64패턴/16카테고리 + LLM 의미분석)
- `im-not-ai` 행 신규 추가 — 외부 AI 스킬 패키지 전역 심링크 설치 패턴
- `graphify` 행 업데이트 — v0.8.36, `graph-update.sh` 빌드 스크립트 방식, `memory.recall`과 역할 분담 명시

### 마이그레이션 노트

- `MEMORY.md`의 `[클럽명]`, `[클럽 설명]` 등 플레이스홀더를 실제 클럽 정보로 교체한다.
- `skillspector` 사용 시 `_system/tools/skillspector/`에 격리 설치 후 `.gitignore` 추가.
- `im-not-ai` 패턴을 따르는 외부 스킬 패키지는 `_system/tools/<패키지명>/`에 설치 후 `./install.sh --claude-only`로 전역 심링크.
- graphify 그래프 빌드는 `bash _system/tools/graphify/graph-update.sh`로 실행.

---

## 2026-06-20 — v1.9.1: slide-prep 스타일 선택 Step 0 추가 + MAP-드롭 0032

### 변경 내용

**수정 스킬 (1종)**
- `slide-prep`: Step 0 (스타일 선택) 신규 추가 — 덱 단위로 스타일(스탠다드·손그림·미니멀모노·브랜드라이트·레고) 하나를 먼저 고르고 기본 모드를 자동 결정하는 흐름으로 개선. Step 2 도입부가 Step 0 결정을 참조하도록 업데이트.

**수정 맵 (1종)**
- `MAP-드롭`: drop-0032(`260616-drop-0032-naver-internship-2026`) 그래프 링크 추가.

### 마이그레이션 노트

- `slide-prep` 사용 시 덱 작업 전 Step 0에서 스타일부터 선정한다. 스타일 상세 및 결정 트리는 `04_studio/brand/system/recipes/slide-styles.md` 참조.
- 손그림 스타일 선택 시 Excalidraw 템플릿(`04_studio/excalidraw/templates/슬라이드-손그림.excalidraw.md`) 분기를 따른다.

---

## 2026-06-13 — v1.9.0: project-close · studio-gallery 신규 + 스튜디오/디자인 인프라 확장

### 변경 내용

**신규 스킬 (2종)**
- `project-close`: 프로젝트(PRJ-*) 종료 마무리 — PROGRESS 최종화 + 허브 정합 + 메모리 갱신
- `studio-gallery`: 스튜디오 코크핏 — 시각 산출물·레퍼런스 스캔 → 라이브 갤러리

**신규 템플릿 (2종)**
- `tpl-디자인러닝`, `tpl-디자인브리프`: 디자인 작업·외주 브리프 표준

**신규 MOC**: `MOC-온보딩` (운영팀 3단계 온보딩 허브)

**신규 레퍼런스 (3종)**: handoff 방법론, oh-my-claudecode 간단설명서, Figma Auto Layout 노하우

**수정 스킬 (8종)**: drop-announce, drop-publish, platform-setup, release, session-announce, session-close, session-thread-open, slide-prep
- drop-publish / session-thread-open: MCP 버전 → 스크립트 버전으로 정본 교체
- release: GitHub release + Discord 공지 단계 확장

**수정 에이전트 (3종)**: prep, publish, studio-team (studio-team에 Mode 0·D 추가, 서브에이전트 표 갱신)

**수정 템플릿 (4종)**: tpl-drop, tpl-세션기록, tpl-세션기획, tpl-연구노트

**Obsidian 인프라 갱신**
- MAP-도구·드롭·스킬·에이전트: 최신 스킬·에이전트 7종 반영
- MOC-세션·스튜디오·시스템·운영·프로젝트·홈: 현황 + 브랜드/코크핏 허브 반영
- 칸반-드롭·메인·세션, design/칸반시스템-구현계획: 정합 갱신
- indexes 2종 (파일명-코드-참조규칙, 폴더-라우팅-인덱스)

**도구 레지스트리**: `_system/tools/README.md` 범용 도구 표 확장 (nlm 제외 유지)

### 마이그레이션 노트

- studio-team 에이전트 Boot 시퀀스가 `DESIGN_SYSTEM.md`를 참조하도록 변경 — 각 조직의 디자인 시스템 문서 경로 확인 필요
- studio-gallery: `studio-manifest.js` 생성 후 `studio.html`에서 라이브 갤러리 사용
- drop-publish / session-thread-open: 스크립트 버전으로 전환됨 — 기존 MCP 버전 의존 워크플로 점검

---

## 2026-06-09 — v1.8.0: 세션 후처리 스킬 9종 + graphify + handoff + attendance.base

### 변경 내용

**신규 스킬 (9종)**
- `session-close` — 세션 종료 마무리 (폴더 감사 + 허브·MOC 링크 갱신 + 메모리 갱신)
- `session-package` — 멤버 공유 패키징 (`member-share-space/` 폴더 생성·복사·README 작성)
- `session-research` — 세션 발표 재료 리서치 (plan 읽기 → 주제별 리서치 → materials/ 저장)
- `session-review` — 세션 후 운영팀 리뷰 초안 생성
- `session-thread-open` — 세션 당일 Discord 스레드 오프닝 메시지 전송
- `session-transcript` — 녹음본 2-track 변환 (정제 스크립트 + 세션 정리)
- `session-announce` — 세션 공지 자동화 (포스터 PNG 캡처 + Discord 초안)
- `graphify` — 폴더·문서를 지식 그래프로 변환 (interactive HTML + GraphRAG JSON)
- `handoff` — 세션 간 컨텍스트 바톤 (휘발성 핸드오프, 메모리OS와 독립)

**신규 Base**
- `_system/obsidian/bases/supporting/attendance.base` — 세션 출석 동적뷰
- `_system/obsidian/bases/supporting/drops.base` — Drop 동적뷰

**수정 스킬 (6종)**
- `session-ops` — 서브커맨드 플로우 개선
- `drop-announce`, `drop-publish` — 발행 워크플로우 정비
- `platform-setup`, `release` — 설정 가이드 갱신
- `.claude/skills/MAP-스킬.md` — 신규 스킬 목록 반영

**수정 에이전트 (1종)**
- `studio-team` — 시각화·배포 파이프라인 개선

**수정 템플릿 (2종)**
- `tpl-세션기록.md`, `tpl-회의록.md` — frontmatter 및 섹션 갱신

**Obsidian 인프라 갱신**
- MAP-스킬, MAP-드롭, MAP-멤버, MAP-도구: 최신 스킬 목록 반영
- MOC-세션, MOC-운영, MOC-프로젝트: 현황 갱신
- 칸반-세션, 칸반-메인: 진행 상황 반영

### 마이그레이션 노트

- 신규 스킬은 `.claude/skills/{name}/SKILL.md` 구조로 추가됨 (session-announce, graphify 포함)
- `session-transcript`: `[녹음 앱]` 플레이스홀더를 실제 녹음 앱명(예: Clovanote)으로 교체
- `graphify`: 별도 Python 패키지 설치 필요 — SKILL.md의 설치 가이드 참조
- `session-close` / `session-review` / `session-package` / `session-research` / `session-thread-open`: session-ops 워크플로우와 연계하여 사용

---

## 2026-05-21 — v1.7.0: Syncthing 가이드, versioning 정책, 폴더 허브, MOC/MAP 정비

### 변경 내용

**신규 가이드 (01_ops/guides/)**
- `260422-versioning-정책.md` — Semver 기반 볼트 릴리즈 버전 관리 정책
- `260422-Syncthing-팀원온보딩.md` — Syncthing 팀원 연결 단계별 가이드
- `260422-Syncthing-협업-인수인계.md` — Syncthing 협업 설정 상태 및 운영 규칙
- `guides.md` — `01_ops/guides/` 폴더 허브 (Dataview 연결)

**신규 템플릿 (01_ops/templates/)**
- `templates.md` — `01_ops/templates/` 폴더 허브 (Dataview 연결)

**볼트 구조 개선**
- `_system/rules.md`: "파일명 규칙 예외" 섹션 추가 (Claude Code skills, superpowers, tools 파일명 면제)
- `_system/obsidian/design/design.md`: Obsidian 설계 문서 허브 신규
- `_system/obsidian/README.md`: design/ 폴더 허브 진입점 추가
- `_system/obsidian/tag-registry.md`: 스텁에서 네임스페이스 구조 포함 파일로 교체

**MOC / MAP 정비**
- `MOC-운영.md`: "폴더 진입점" 섹션 추가 (guides, templates, members, meetings, reviews, strategy, posts)
- `MOC-세션.md`: 테이블 형식으로 업데이트 + 세션명 일반화
- `MOC-프로젝트.md`: "프로젝트 현황" 테이블 구조 업데이트
- `MAP-스킬.md`: framework-sync, nlm-skill 항목 제거
- `MAP-드롭.md`: drop-0022 추가, Notion URL/Discord ID 플레이스홀더화
- `MAP-플랫폼.md`: Syncthing 섹션 추가

**칸반 업데이트**
- `칸반-메인.md`: PRJ-볼트v3 완료 처리, PRJ-노션DB세팅 보류 이동
- `칸반-세션.md`: 세션명 일반화

**템플릿 수정**
- `tpl-drop.md` / `tpl-연구노트.md` / `tpl-멤버프로필.md`: 일반화 및 frontmatter 수정

### 마이그레이션 노트

- Syncthing 가이드의 `[관리자-DEVICE-ID]`, `[FOLDER-ID]`, `[볼트경로]` 등 플레이스홀더를 실제 값으로 교체해야 한다.
- `MAP-드롭.md`의 `[Notion-드롭-DB-URL]`, `[Discord-채널-ID]`를 실제 값으로 교체해야 한다.

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
