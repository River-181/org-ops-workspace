---
name: vault-health
description: 네이밍·메타데이터·링크·깊이 위생 점검
---

# 볼트 점검

## 목적

[볼트명] 볼트의 구조적 건강 상태를 점검하고 문제를 보고한다.

## 절차

### 1. 파일명 규칙 점검
- 모든 .md 파일이 `YYMMDD-제목.md` 형식을 따르는지 확인한다.
- 예외: `tpl-`, `README.md`, `CLAUDE.md`, `SKILL.md`, `MAP-스킬.md`, `S{NN}-*-plan.md`, `S{NN}-*-record.md`
- 위반 파일 목록을 작성한다.
- 구 네이밍(`OPS-DOC-`, `_live`, `_arch` 접미사) 잔존 여부를 확인한다.

### 2. Frontmatter 점검
- 모든 .md 파일의 frontmatter에 `title`, `status`, `created`가 있는지 확인한다.
- `status` 값이 `draft | live | archived` 중 하나인지 확인한다.
- 구 frontmatter 필드(id, area, unit, visibility, state, version) 잔존 확인.

### 3. 폴더 깊이 점검
- 5레벨 초과 경로를 탐지한다.
- `_system/rules.md`의 깊이 규칙 참조.

### 4. 빈 파일/폴더 탐지
- frontmatter만 있고 본문이 없는 파일.
- .md 파일이 하나도 없는 폴더 (assets 폴더 제외).

### 5. 위키링크 점검
- `[[깨진링크]]` 탐지 (대상 파일이 존재하지 않는 경우).

### 6. 중첩 복사본 감지
- `05_library/seasons/` 내부에 현재 구조와 동일한 중첩 복사본이 있는지 확인한다.

### 7. 절대경로 감지
- `.claude/` 및 `_system/` 파일에서 `/Users/` 등 절대경로가 있는지 확인한다.

### 8. `up:` 누락 점검
- 볼트 전체 활성 .md 파일에서 `up:` 필드가 없는 파일을 탐지한다.
- 아래 명령으로 `up:` 없는 파일 목록을 추출한다:
  ```bash
  # up: 필드가 있는 파일 목록
  grep -rl "^up:" . --include="*.md" > /tmp/has_up.txt
  # 전체 .md 파일 목록과 비교
  ```
- **예외** (up: 없어도 정상인 파일):
  - `CLAUDE.md`, `README.md` (볼트 루트)
  - `MOC-홈.md` (최상위 허브, 의도적으로 up: 없음)
  - `01_ops/templates/tpl-*.md` (템플릿은 빈 상태가 정상)
  - `05_library/seasons/2025-pre/` 아카이브 전체
- 위반 파일은 CRITICAL로 보고.

### 9. 고아 파일 점검
- 다른 어떤 파일에서도 `[[wikilink]]`로 참조되지 않는 파일을 탐지한다.
- 아래 절차로 확인:
  1. 대상 파일명(확장자 제외)을 추출
  2. `grep -r "\[\[파일명\]\]"` 으로 역참조 존재 여부 확인
  3. 역참조 0개인 파일 = 고아
- Obsidian CLI 활용:
  ```bash
  obsidian backlinks file="파일명"
  ```
  backlinks가 없으면 고아로 판정.
- **예외**: `MOC-홈.md`, `CLAUDE.md`, `README.md` (루트), `INDEX-MEMBER.md`
- 고아 파일은 WARNING으로 보고 (삭제 아님 — 링크 추가 권고).

### 10. 태그 레지스트리 검증
- 볼트 전체에서 사용된 태그를 수집하고 `_system/obsidian/tag-registry.md`에 등록된 태그와 비교한다.
- 수집 방법:
  ```bash
  # frontmatter의 tags: 배열에서 수집
  grep -rh "^  - " . --include="*.md" | grep -E "^\s+- [a-z]" | sort -u
  # 또는 Obsidian CLI
  obsidian tags sort=name
  ```
- **미등록 태그**: 레지스트리에 없는 태그 → WARNING
- **사용 안 된 태그**: 레지스트리에는 있지만 볼트에서 0번 사용 → INFO
- `05_library/seasons/2025-pre/` 아카이브의 태그는 검사 제외.

### 11. 보고서 생성
- 출력: `01_ops/reviews/YYMMDD-볼트점검.md`
- 카테고리별 위반 수 + 구체적 파일 목록
- 심각도: CRITICAL / WARNING / INFO
- 섹션 순서:
  1. 요약 (심각도별 총 건수)
  2. CRITICAL 항목 (파일명 규칙, frontmatter 필수 필드, `up:` 누락)
  3. WARNING 항목 (고아 파일, 깨진 위키링크, 미등록 태그)
  4. INFO 항목 (빈 파일, 사용 안 된 태그, 기타)

## 주의사항

- `05_library/seasons/2025-pre/` 아카이브는 구명칭이 남아 있어도 WARNING으로만 보고.
- `_system/` 거버넌스 파일(CLAUDE.md, rules.md, identity/ 폴더)은 직접 수정하지 않고 보고만 한다.
