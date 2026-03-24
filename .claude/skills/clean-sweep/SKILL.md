---
name: clean-sweep
description: 볼트 위생 정리 — Icon 파일, 빈 폴더, .DS_Store, 중복 파일 탐지 및 삭제 제안
---

# 위생 정리

## 목적

볼트 전체에서 불필요한 파일과 폴더를 탐지하고 삭제를 제안한다.

## 절차

### 1. Icon 파일 탐지

macOS Finder 아이콘 캐시(`Icon\r`) 파일을 전체 볼트에서 검색한다.
발견된 파일 수를 보고한다.

### 2. .DS_Store 탐지

`.DS_Store` 파일을 전체 볼트에서 검색한다.

### 3. 빈 폴더 탐지

`.md` 파일도, 의미 있는 파일도 없는 빈 폴더를 탐지한다.
`.gitkeep`만 있는 폴더는 빈 폴더로 간주하지 않는다.

### 4. 루트 떠돌이 파일 탐지

볼트 루트에 있지만 `_system/`, `01_ops/` ~ `06_inbox/` 어디에도 속하지 않는 `.md` 파일을 탐지한다.
예외: `CLAUDE.md`, `README.md`

### 5. 중복 파일 의심 탐지

파일명이 유사하면서 다른 폴더에 존재하는 파일을 찾는다.
예: `01_ops/recruit/260312-모집공고.md` ↔ `04_studio/posts/260312-모집공고.md`

### 6. .gitignore 점검

`.gitignore`에 다음 패턴이 포함되어 있는지 확인한다:

```
Icon?
.DS_Store
*.log
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.obsidian/graph.json
```

누락된 패턴이 있으면 추가를 제안한다.

### 7. 보고서 + 삭제 제안

```
## 위생 정리 보고

### 요약
- Icon 파일: N개
- .DS_Store: N개
- 빈 폴더: N개
- 루트 떠돌이: N개
- 중복 의심: N쌍
- .gitignore 누락 패턴: N개

### 삭제 제안
| 대상 | 유형 | 제안 |
|------|------|------|
```

**사용자 승인을 받은 후에만 삭제를 실행한다.**

## 주의사항

- `05_library/seasons/2025-pre/` 내부의 아카이브 파일은 중복으로 보고하지 않는다.
- 삭제 전 반드시 사용자 확인을 받는다.
