---
title: 워크스페이스 운영 규칙
kind: system
area: system
status: live
created: 2026-03-14
up: "[[MOC-시스템]]"
tags:
  - system/schema
---

# 워크스페이스 운영 규칙

## 1. 폴더 구조

### 최상위 (7개)

| # | 폴더 | 역할 |
|---|------|------|
| — | `_system/` | 에이전트 설정, 규칙, 플랫폼 설정 |
| 01 | `01_ops/` | 운영 전반 (전략, 회의, 모집, 재정, 멤버, 템플릿) |
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
OK:  02_sessions/S01-시작/materials/slides.pdf     (4레벨)
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
| `260320-시즌1모집공고.md` | `01_ops/recruit/` |
| `260401-PKM방법론비교.md` | `05_library/research/methods/` |

### 특수 접두사

| 접두사 | 용도 | 예시 |
|--------|------|------|
| `tpl-` | 템플릿 | `tpl-세션기록.md` |
| `PRJ-` | 프로젝트 폴더 | `PRJ-디스코드봇/` |

> 세션 폴더 네이밍은 `_system/identity/workflow.md`의 `subfolder_pattern`을 따른다.

### 하지 말 것

- ~~`OPS-DOC-260311-01_T2_`~~ 코드 조합 네이밍
- ~~`_live`, `_arch`, `_draft`~~ 파일명 접미사 → frontmatter `status:`로 대체
- ~~공백~~ 파일명에 공백 대신 하이픈(-) 사용

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
| `session/` | `session/plan`, `session/record` |
| `project/` | `project/PRJ-이름` |
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

플랫폼 설정은 `_system/identity/platform.md`를 참조한다. (조직에 맞게 설정)

**원칙**: 로컬에서 작성 → 외부에 요약 게시 → 멤버가 소비
