---
title: 진단 감사 저장 규약
kind: index
area: system
status: live
created: 2026-04-16
tags: [system/audit, system/standard]
up: "[[MOC-시스템]]"
---

# 진단 감사 저장 규약

> `_system/obsidian/audits/` 폴더는 볼트 진단 스킬의 실행 결과를 저장하는 장기기억 자원이다.

---

## 1. 목적

이 폴더는 볼트 진단 스킬(vault-health, link-audit, tag-audit, frontmatter-scan, manifest-check, verify-implementation) 실행 결과를 저장한다.

- **장기기억 자원**: 이전 진단과 현재 상태를 비교하여 개선 추이를 추적한다.
- **이행 추적**: 발견된 문제가 다음 진단 전에 해결되었는지 체크박스로 확인한다.
- **운영 근거**: 구조 변경 결정의 근거 자료로 활용한다.

---

## 2. 파일 네이밍

### 일회성 진단

```
YYMMDD-{진단종류}.md
```

예시:
- `260416-vault-health.md`
- `260416-link-audit.md`
- `260416-verify-implementation.md`

같은 날 동일 스킬을 두 번 실행한 경우 `260416-vault-health-2.md`처럼 회차 접미사를 추가한다.

### 라이브 대시보드

```
{주제}-감사.md
```

예시: `볼트-감사.md`, `라이브러리-감사.md`

Dataview 쿼리 기반으로 상시 자동 갱신된다. 수동 편집하지 않는다.

---

## 3. Frontmatter 표준

```yaml
---
title: YYMMDD {진단종류} — 짧은 요약
kind: audit
area: system
status: live
created: YYYY-MM-DD
source_skill: vault-health | link-audit | tag-audit | frontmatter-scan | manifest-check | verify-implementation
tags: [system/audit]
up: "[[MOC-시스템]]"
---
```

### 필드 설명

| 필드 | 필수 | 설명 |
|------|------|------|
| `title` | 필수 | `YYMMDD 진단종류 — 요약` 형식 |
| `kind` | 필수 | 항상 `audit` |
| `area` | 필수 | 항상 `system` |
| `status` | 필수 | 항상 `live` (아카이빙 전까지) |
| `created` | 필수 | 진단 실행 날짜 |
| `source_skill` | 필수 | 진단을 생성한 스킬 이름 |
| `tags` | 필수 | 최소 `system/audit` 포함 |
| `up` | 필수 | `[[MOC-시스템]]` |

---

## 4. 본문 섹션 (순서 고정)

아래 6개 섹션을 순서대로 작성한다. 해당 내용이 없는 섹션도 제목과 "없음"을 명시한다.

### 1) 요약

전체 상태를 3~5줄로 서술한다. CRITICAL·WARNING·INFO 건수를 명시한다.

```
- CRITICAL: N건
- WARNING: N건
- INFO: N건
```

### 2) CRITICAL

즉시 조치가 필요한 항목. 파일명 규칙 위반, 필수 frontmatter 누락, 깨진 링크, 표준 위반 등.

없으면: `없음`

### 3) WARNING

권장 조치 항목. `draft` 상태 장기 방치, 고아 파일, 미등록 태그 등.

없으면: `없음`

### 4) INFO

통계 및 관찰 사항. 태그 분포, 상태 분포, 구조 현황 등.

### 5) 이행 항목

이 진단으로부터 파생된 구체적 작업을 `- [ ]` 체크박스로 나열한다.

```markdown
- [ ] `02_sessions/S05-*/plan.md` — `up:` 필드 추가
- [ ] `01_ops/meetings/YYMMDD-*.md` — `kind: meeting` 추가
```

다음 진단 시 완료 여부를 확인한다.

### 6) 관련 링크

```markdown
- 다음 정기 진단 예정: YYYY-MM-DD
- 이전 보고서: [[YYMMDD-{진단종류}]]
- 관련 보고서: [[YYMMDD-{다른진단}]]
```

---

## 5. 실행 주기

| 구분 | 주기 | 비고 |
|------|------|------|
| 정기 진단 | 월 1회 | `weekly-review`와 묶어 첫째 주 일요일 |
| 대형 구조 변경 후 | 1회 | Phase 완료 시마다 |
| 라이브 대시보드 | 상시 | Dataview 자동 갱신 |

`verify-implementation` 스킬은 4개 진단 스킬을 한 번에 실행하므로 정기 진단 시 우선 사용한다.

---

## 6. 스킬별 저장 파일명 빠른 참조

| 스킬 | 저장 파일명 예시 |
|------|----------------|
| `vault-health` | `260416-vault-health.md` |
| `link-audit` | `260416-link-audit.md` |
| `tag-audit` | `260416-tag-audit.md` |
| `frontmatter-scan` | `260416-frontmatter-scan.md` |
| `manifest-check` | `260416-manifest-check.md` |
| `verify-implementation` | `260416-verify-implementation.md` |

---

## 7. 삭제 / 아카이빙

- **6개월 이상** 경과한 일회성 진단 파일은 `_system/obsidian/audits/archive/YYYY/`로 이동 가능하다.
- CRITICAL 이슈 이력이 있는 파일은 이행 항목 미완료 시 삭제 금지.
- 라이브 대시보드(`-감사.md`)는 삭제하지 않는다.
