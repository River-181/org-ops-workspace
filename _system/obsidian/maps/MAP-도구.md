---
title: MAP-도구
kind: map
area: system
status: live
created: 2026-03-24
up: "[[MOC-시스템]]"
tags:
  - system/tools
  - type/map
---

# MAP-도구

> 이 조직이 사용하는 외부 CLI/MCP 도구의 Obsidian 허브.
> 에이전트용 레지스트리: [[_system/tools/README|_system/tools/README]]

---

## 도구 목록

| 도구 | 인덱스 | 분류 | Claude Code 스킬 |
|------|--------|------|-----------------|
| (도구 추가 시 여기에) | — | — | — |

---

## 도구 추가 패턴

새 도구가 생길 때 아래 순서를 따른다.

### 1. 폴더 및 파일 생성

```
_system/tools/{도구명}/
├── {도구명}.md              ← 이 파일 (인덱스, Obsidian 허브)
├── YYMMDD-{도구명}-운영가이드.md   ← 에이전트용 상세 가이드
└── {인증·데이터 폴더}/       ← .gitignore 제외
```

### 2. 레지스트리 업데이트

- 위 도구 목록 표에 행 추가
- `_system/tools/README.md` 도구 목록 업데이트
- `_system/tools/.env`에 경로 환경변수 추가
- `_system/tools/.gitignore`에 인증 데이터 경로 추가

### 3. 스킬 생성

```
.claude/skills/{도구명}/
└── SKILL.md    ← frontmatter: name, description, allowed-tools
```

- `.claude/skills/MAP-스킬.md` 스킬 목록 업데이트
- `CLAUDE.md` 스킬 섹션 업데이트

---

## 자동 목록 (Dataview)

```dataview
TABLE title, status
FROM "_system/tools"
WHERE file.name != "MAP-도구" AND file.name != "README" AND file.extension = "md"
SORT file.name ASC
```
