---
name: reference-save
description: 조사 결과를 _system/reference/에 저장하고 인덱스(README)를 갱신
argument-hint: "[주제명]"
---

# 레퍼런스 저장

## 목적

현재 세션에서 조사한 내용을 `_system/reference/`에 저장하고, `README.md` 인덱스를 갱신한다.

## 입력

- **주제명**: 저장할 레퍼런스의 주제 (예: `obsidian-dataview-api`)

인자가 없으면 사용자에게 주제명과 카테고리를 물어본다.

## 절차

### 1. 카테고리 결정

저장할 내용에 맞는 카테고리를 선택한다:

| 카테고리 | 폴더 | 해당 내용 |
|---------|------|----------|
| claude-code | `_system/reference/claude-code/` | Claude Code 포맷·동작·API |
| obsidian | `_system/reference/obsidian/` | Obsidian 플러그인·기능·문법 |
| tools | `_system/reference/tools/` | 외부 도구 (Google Drive, Notion 등) |

카테고리에 맞지 않으면 가장 가까운 것을 선택하고 사용자에게 알린다.

### 2. 파일 생성

`_system/reference/{카테고리}/YYMMDD-{주제명}.md`로 저장한다.

**frontmatter 형식:**

```yaml
---
title: "{주제명}"
kind: reference
area: system
status: live
created: {오늘 날짜}
tags:
  - system/reference
  - platform/{관련 플랫폼 또는 도구}
up: "[[_system/reference/README]]"
source: "{출처 — 공식 문서 URL, 조사 날짜 등}"
---
```

**본문 구조 (권장):**

```markdown
# {주제명}

> {한 줄 요약}. {조사 날짜} 기준.

---

## 주요 내용

...

## 주의사항

...

## 참고

- {URL 또는 출처}
```

### 3. README 인덱스 갱신

`_system/reference/README.md`의 해당 카테고리 테이블에 새 행을 추가한다:

```markdown
| [[파일명]] | {주제 설명} | {조사일} |
```

### 4. 확인

저장된 파일 경로와 인덱스 갱신 내용을 사용자에게 보여준다.

## 주의사항

- 레퍼런스는 **사실 기록**이다. 의견이나 계획이 아닌 조사된 정보만 저장한다.
- 시간이 지나면 내용이 outdated될 수 있다. `updated:` 필드를 갱신할 것.
- 기존 레퍼런스와 주제가 겹치면 새 파일 생성 대신 기존 파일 업데이트를 권장한다.
