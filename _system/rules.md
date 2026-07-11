---
title: 워크스페이스 운영 규칙
kind: system
area: system
status: live
created: 2026-03-14
updated: 2026-03-28
up: "[[MOC-시스템]]"
tags:
  - system/schema
---

# [볼트명] 운영 규칙

## 1. 폴더 구조

### 최상위 (7개)

| # | 폴더 | 역할 |
|---|------|------|
| — | `_system/` | 에이전트 설정, 규칙, 플랫폼 설정 |
| 01 | `01_ops/` | 운영 전반 (전략, 회의, 재정, 멤버, 템플릿) |
| 02 | `02_sessions/` | 정기·비정기 세션 (기획, 자료, 기록) |
| 03 | `03_projects/` | 프로젝트 (운영/멤버, 태그로 구분) |
| 04 | `04_studio/` | 브랜드 에셋, 디자인 소스 |
| 05 | `05_library/` | 리서치, 발행물 보관, 시즌 아카이브 |
| 06 | `06_inbox/` | 분류 전 임시 파일 |

### 깊이 규칙

- **최대 5레벨** 허용
- **3레벨 권장** (최상위 → 카테고리 → 파일)
- 4-5레벨은 프로젝트/세션 내부에서만 허용

```
OK:  02_sessions/S01-[세션명]/materials/slides.pdf     (4레벨)
BAD: 01_ops/strategy/2026/spring/week1/draft/memo.md       (7레벨)
```

---

## 2. 파일 네이밍

### 기본 형식

```
YYMMDD-제목.확장자
```

### 예시

| 파일명 | 위치 |
|--------|------|
| `260314-시즌1킥오프회의.md` | `01_ops/meetings/` |
| `260320-시즌1모집공고.md` | `04_studio/posts/` |
| `260401-PKM방법론비교.md` | `05_library/research/methods/` |

### 특수 접두사

| 접두사 | 용도 | 예시 |
|--------|------|------|
| `tpl-` | 템플릿 | `tpl-세션기록.md` |
| `S{NN}-` | 세션 폴더 | `S01-내우주의시작/` |
| `PRJ-` | 프로젝트 폴더 | `PRJ-디스코드봇/` |
| `RES-` | 리서치 허브 파일 | `RES-시간관리.md` |

### 단일 허브 파일

- 프로젝트 허브: `03_projects/PRJ-이름/PRJ-이름.md`
- 리서치 허브: `05_library/research/.../RES-주제명.md`
- MOC에서 이 허브들을 링크할 때는 가급적 `[[PRJ-이름]]`, `[[RES-주제명]]`처럼 파일 원제목 그대로 단다

### MOC / MAP / Base / Audit

- MOC는 얇은 수동 허브다. 핵심 진입점과 큐레이션만 둔다.
- MAP은 Base 중심의 동적 탐색 허브다. 큰 폴더, 많은 문서, 운영용 집계는 MAP에서 다룬다.
- Base는 카탈로그다. 폴더 단위 목록, 상태 표, 필터·정렬은 Base가 담당한다.
- 루트 `_system/obsidian/bases/`에는 핵심 Base만 둔다: `projects.base`, `sessions.base`, `library.base`, `tools.base`, `inbox.base`
- 덜 중요한 세부 운영 Base는 `_system/obsidian/bases/supporting/` 아래로 내린다.
- 가능하면 한 Base 파일 안에 여러 유용한 view를 넣는다. `tools.base`처럼 `catalog`, `grouped`, `gallery`를 한 파일에서 해결하는 방식을 기본값으로 삼는다.
- 루트 Base는 가능한 한 `대표 허브 파일 1개 = 1행` 원칙을 따른다.
- 예외는 `library.base`로, 라이브러리 허브와 article/reference 발행물을 함께 다룬다.
- Audit은 Dataview 기반의 감사·집계·예외 탐지용 노트다. 1차 진입점으로 쓰지 않는다.
- 감사 노트는 `_system/obsidian/audits/` 아래에 둔다. Dataview 통계와 누락 탐지는 감사 노트가 맡는다.
- MOC에서는 raw path 나열을 피하고, 가능한 한 실제 노트나 Base 파일로 링크한다.
- MOC에서는 과도한 커스텀 링크 텍스트를 피하고, 파일 원제목 링크를 기본으로 한다.
- MOC에서는 중첩 리스트와 대형 정적 표를 기본값으로 쓰지 않는다. 작은 규모의 안정적 요약 표만 예외로 허용한다.
- MAP에서는 Dataview 블록을 직접 넣기보다, 가능하면 대응 Base 파일을 링크한다. Base가 없을 때만 예외를 문서화한다.

### 도구 허브 규칙

- 도구 노트(`kind: tool`)의 부모는 폴더 `README`가 아니라 `[[MAP-도구]]`다.
- 도구 상세 운영가이드(`kind: guide`)의 부모는 해당 도구 노트다.
- `_system/tools/README.md`는 운영 레지스트리다. 상위 허브가 아니라 설명과 체크리스트를 담는다.
- 큰 도구 목록과 상태 뷰는 `_system/obsidian/bases/tools.base`가 담당한다.

### 하지 말 것

- ~~`OPS-DOC-260311-01_T2_`~~ 코드 조합 네이밍
- ~~`_live`, `_arch`, `_draft`~~ 파일명 접미사 → frontmatter `status:`로 대체
- ~~공백~~ 파일명에 공백 대신 하이픈(-) 사용

### 파일명 규칙 예외

외부 시스템과 도구는 파일명 네이밍 규칙 면제:

- `.claude/skills/*/SKILL.md` — Claude Code 플랫폼 규약, `YYMMDD-` 접두사 불가
- `.claude/skills/*/references/**` — 벤더링된 외부 스킬 참조 파일, 원 포맷 유지
- `docs/superpowers/**` — 외부 프로토콜 문서, 외부 포맷 유지
- `_system/tools/*` — 도구별 진입점 파일명 자유 (예: `GitHub.md`, `figma.md`, `syncthing.md`)
- PRJ 루트 `PROGRESS.md`, `PLAN.md` — 프로젝트 메타 관례 허용

### 버전 관리

- 기본: 버전 표기 없음 (최신이 진실)
- 필요 시: `-v02` 접미사 (예: `260314-기획서-v02.md`)
- 구버전은 archive로 이동하거나 git으로 관리

---

## 3. Frontmatter 표준

### 필수

```yaml
---
title: 문서 제목
status: draft | live | archived
created: YYYY-MM-DD
---
```

### 선택

```yaml
updated: YYYY-MM-DD
tags:
  - namespace/tag
related:
  - "[[다른문서]]"
```

### 태그 네임스페이스

| 네임스페이스 | 예시 |
|-------------|------|
| `ops/` | `ops/strategy`, `ops/meeting`, `ops/recruit`, `ops/finance` |
| `session/` | `session/S01`, `session/S02` |
| `project/` | `project/PRJ-디스코드봇` |
| `research/` | `research/pkm`, `research/tools`, `research/ai` |
| `season/` | `season/2026-S1`, `season/S2` |
| `type/` | `type/ops`, `type/member` (프로젝트 구분용) |

---

## 4. 워크플로우

### 새 파일 만들기

1. 해당 폴더로 이동
2. `YYMMDD-제목.md` 형식으로 생성
3. frontmatter 포함 (템플릿 있으면 템플릿 사용)
4. `status: draft`로 시작 → 완료 시 `live`로 변경

### 인박스 처리

1. 분류 안 되는 파일 → `06_inbox/`에 일단 넣기
2. 주 1회 이상 인박스 비우기 (적절한 폴더로 이동)
3. 인박스에 2주 이상 방치된 파일 → 삭제 또는 archive

### 아카이빙

1. 시즌 종료 시 → `05_library/seasons/YYYY-SN/`에 스냅샷
2. `status: archived`로 변경
3. 원본은 제자리에 유지하되 archived 상태로 표시

---

## 5. 플랫폼 역할

| 툴 | 역할 | 저장 대상 |
|----|------|----------|
| **로컬 (Obsidian)** | SoT, 기획, 집필 | 모든 원본 문서 |
| **Notion** | 협업, 공유 대시보드 | 세션 요약, 멤버 DB, 자료 링크 |
| **Google Drive** | 대용량 파일 | 영상, 원본 PDF, PPT |
| **Discord** | 실시간 소통 | 공지, Q&A |
| **Figma** | 디자인 | 브랜딩 자산 |

**원칙**: 로컬에서 작성 → Notion/Discord에 요약 게시 → 멤버가 소비

---

## 6. AX 운영 규칙 (도입 시 활성화)

> [클럽명] AX 전환에 따른 AI 사령탑 운영의 핵심 규칙. 상세 → [[260621-AX-사령탑-운영-프로토콜]]

### AI 사령탑 위상

AI 사령탑(LLM 에이전트, 24/7)은 **AX 총괄 사령탑**이다. 보조 도구가 아니라 운영 지휘의 중심이다.

```
사람 운영자 (감독·승인) → AI 사령탑 (판단·지시·통합) → Claude Code + 외부도구 (실행)
```

- 사람 운영자([운영자 목록])는 감독·승인·전략에 집중한다
- AI 사령탑은 반복 운영(공지·리마인드·브리핑·기록 유도)을 자율 처리한다
- Claude Code는 AI 사령탑이 위임하는 심층 제작(SoT 편집·설계·슬라이드)을 담당한다

### [볼트명] 볼트 읽기전용 원칙

**AI 사령탑은 [볼트명](SoT)를 직접 편집하지 않는다.**

- AI 사령탑의 볼트 접근: 읽기전용 git 미러(`git pull` cron)
- 볼트 편집: Claude Code 또는 사람 운영자 경유 필수
- 이유: SoT 무결성 보호 + 시크릿 비전파 + git 이력 감사 가능성 유지

### 자가증강 스킬 게이트

AI 사령탑이 생성한 자가증강 스킬 초안은 **사람이 검토·승인한 뒤에만 실행 등록**된다.

- 검토 채널: `#ops-system`
- 게이트: 운영자 ✅ 리액션 = 승인. 미승인 스킬 자동 실행 금지

### OPS-ROOM 협업 프리미티브 (요약)

| 채널 | 용도 |
|------|------|
| `#ops-command` | 운영자 → AI 사령탑 지시 (과제 = 스레드 1개) |
| `#ops-hermes` | AI 사령탑 → 운영자 출력·알림·승인요청 |
| `#ops-system` | AI 사령탑 헬스·에러·자가증강 검토 큐 |

승인 리액션: ✅ 승인 / 👀 검토 중 / ⏳ 대기 / ❌ 반려 / 📌 결정 기록

### 멤버 응답 경계

- AI 사령탑의 멤버 채널 응답은 현재 반자동(운영자 트리거) 방식
- 공개 응답은 공개 자료 기반만. 운영 내부 정보 노출 금지

> 운영팀 온보딩 참조: [[온보딩-Lv1-Day1]] §6 (정신 모형) · [[온보딩-Lv2-Week1]] §7 (OPS-ROOM 운영) · [[온보딩-Lv3-Month1]] §8 (위임·승인 게이트)

---

## 7. 자동화 훅 정책

- `.claude/settings.json`은 볼트 전역 훅 정의. 개인 설정은 `.claude/settings.local.json` (gitignore).
- 신규 훅 추가 시:
  1. `_system/agents/memory/decisions/`에 근거 기록
  2. `.claude/settings.json` 업데이트
  3. 이 섹션에 한 줄 요약 append
