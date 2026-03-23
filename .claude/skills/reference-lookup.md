---
description: _system/reference/에서 특정 주제의 레퍼런스를 검색하여 반환
argument-hint: "[검색어]"
allowed-tools: Read, Glob, Grep
---

# 레퍼런스 조회

## 목적

`_system/reference/`에 저장된 레퍼런스 중 검색어와 관련된 내용을 찾아 반환한다.

## 입력

- **검색어**: 찾고자 하는 주제 (예: `agent frontmatter`, `obsidian dataview`)

인자가 없으면 전체 인덱스(`README.md`)를 반환한다.

## 절차

### 1. 인덱스 확인

`_system/reference/README.md`를 읽어 어떤 레퍼런스가 있는지 파악한다.

### 2. 검색

검색어가 있으면:
1. 인덱스 테이블에서 주제명·파일명 매칭 확인
2. `Grep`으로 `_system/reference/` 전체에서 검색어 포함 파일 탐색

### 3. 결과 반환

**매칭 파일이 있으면**: 해당 파일의 핵심 내용을 요약하여 반환.
**없으면**: "해당 주제의 레퍼런스가 없습니다. `/reference-save`로 추가할 수 있습니다." 안내.

### 4. 복수 결과

여러 파일이 매칭되면 목록을 먼저 보여주고, 사용자가 선택한 것을 상세히 반환한다.

## 주의사항

- 레퍼런스는 조사 시점 기준이다. `created:` 날짜가 오래됐으면 내용이 변경됐을 수 있음을 안내한다.
