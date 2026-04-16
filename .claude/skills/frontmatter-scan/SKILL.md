---
name: frontmatter-scan
description: frontmatter 완성도 빠른 스캔 — 필드별 채택률, 폴더별 준수율 통계
allowed-tools: Read, Glob, Grep
---

# Frontmatter 스캔

## 목적

볼트 전체 .md 파일의 frontmatter 완성도를 빠르게 통계낸다. **파일을 수정하지 않는다.**

## 절차

### 1. 대상 파일 수집

- 전체 볼트에서 `.md` 파일을 수집한다.
- 제외: `.claude/`, `.obsidian/`, `05_library/seasons/2025-pre/`

### 2. 필드별 채택률

각 파일의 frontmatter를 파싱하여 다음 필드의 존재 여부를 확인한다:

| 필드 | 구분 | 설명 |
|------|------|------|
| `title` | 필수 | 문서 제목 |
| `status` | 필수 | draft / live / archived |
| `created` | 필수 | 생성일 |
| `kind` | 권장 | 문서 유형 |
| `area` | 권장 | 소속 도메인 |
| `up` | 권장 | 부모 노트 |
| `tags` | 권장 | 태그 |
| `related` | 선택 | 관련 문서 |
| `updated` | 선택 | 수정일 |

### 3. 보고서 출력

```
## Frontmatter 스캔 보고

### 전체 통계
- 대상 파일: N개
- frontmatter 있음: N개 (N%)
- frontmatter 없음: N개

### 필드별 채택률
| 필드 | 있음 | 없음 | 채택률 |
|------|------|------|--------|

### 폴더별 준수율
| 폴더 | 파일 수 | 필수 100% | kind | area | up | tags |
|------|--------|----------|------|------|-----|------|

### 필수 필드 누락 파일 (즉시 수정 필요)
| 파일 | 누락 필드 |
|------|----------|
```

## 결과 저장

`_system/obsidian/audits/YYMMDD-frontmatter-scan.md` 파일로 저장한다.
frontmatter·섹션 구조는 [[_system/obsidian/audits/README|진단 감사 저장 규약]]을 따른다.

필수 필드:
- `source_skill: frontmatter-scan`
- `kind: audit`
- `area: system`

본문 구조: 요약 → CRITICAL → WARNING → INFO → 이행 항목

## 주의사항

- 진단 전용. 수정은 `@frontmatter-doctor`가 수행한다.
- 이전 스캔 결과와 비교하려면 `_system/obsidian/audits/`에서 이전 보고서를 참조한다.
