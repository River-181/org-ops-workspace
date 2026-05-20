---
title: 버전 관리 정책 (Semver)
kind: guide
area: ops
status: live
created: 2026-04-22
updated: 2026-04-22
up: "[[guides]]"
related:
  - "[[RUNBOOK-볼트유지]]"
  - "[[CHANGELOG]]"
tags:
  - ops/guide
  - ops/release
---

# 버전 관리 정책

> [볼트명] 운영 볼트의 릴리즈 버전 관리 규칙. 2인 협업 단계부터 적용.
> 상위: [[guides]] · 관련: [[RUNBOOK-볼트유지]], [[CHANGELOG]]

---

## 1. 원칙

`v<MAJOR>.<MINOR>.<PATCH>` 형식의 Semantic Versioning을 운영 볼트 맥락에 맞게 재정의.

| 자리 | 뜻 | 예시 |
|------|----|----|
| **MAJOR** | 볼트 구조·Workspace OS 자체의 대변경. 기존 문서/스킬/에이전트 사용법 영향 있음. | `v1.0` (Workspace OS v3 출범), `v2.0` (OS v4 마이그레이션) |
| **MINOR** | 신규 기능 추가. 스킬 · 플랫폼 · MOC · MAP · 주요 가이드 신설. 기존 사용법은 그대로 작동. | `v0.10` (Syncthing 협업), `v0.7` (장기기억 시스템) |
| **PATCH** | 내용 수정 · frontmatter 보강 · 오타 · 링크 교정 · 소규모 정비. 구조 변화 없음. | `v0.5.1` (`.git` Icon 정리) |

---

## 2. 판단 기준 체크리스트

PR/릴리즈 분류 시 아래 순서로 확인.

### MAJOR (드물게)

- [ ] 폴더 구조 최상단이 바뀌는가? (`01_ops/`·`02_sessions/` 등 rename / 대체)
- [ ] Frontmatter 계약이 새 필수 필드를 추가하는가?
- [ ] 기존 스킬 · 에이전트 호출 방식을 깨는가?
- [ ] 기존 태그 레지스트리 스키마 대변경?

하나라도 **예**이면 MAJOR.

### MINOR

- [ ] 새 스킬 / 에이전트 / MOC / MAP / 가이드 신설?
- [ ] 새 플랫폼 · 툴 레지스트리 등록?
- [ ] 새 세션(`S0N-*`) · 시즌 · 프로젝트 시작?
- [ ] 기존 규칙에 **선택적** 확장 (기존 문서 영향 없음)?

하나라도 **예**이면 MINOR.

### PATCH

- 위 두 조건에 해당하지 않는 나머지 전부
- 진단 후 CRITICAL 자동 정비, 오타 수정, 링크 교정, status 조정 등

---

## 3. 프리릴리즈 · 예외

- **v0.x** 전체는 "베타" 단계로 간주. `v1.0` 도달 전까지 MINOR·MAJOR 경계 완화(구조 변경도 MINOR로 허용).
- **긴급 패치**는 `vX.Y.Z` 증가. 예: `v0.5.1`.
- 같은 날 다수 릴리즈 시 시각 순으로 넘버링 (예: `v0.10` → `v0.11`).

---

## 4. 릴리즈 체크리스트 (operator)

순서:

1. [ ] `.claude/skills/release` 스킬 또는 수동 절차
2. [ ] `_system/agents/memory/change-log.md` 상단에 새 날짜 섹션 append
3. [ ] 루트 `CHANGELOG.md` 해당 버전 섹션 추가
4. [ ] `git add` 선별 → commit (커밋 메시지: `release: vX.Y — 한 줄 요약`)
5. [ ] `git push origin main`
6. [ ] `gh release create vX.Y` (타이틀 `vX.Y — YYYY-MM-DD 한줄`, 본문 CHANGELOG 복사)
7. [ ] `01_ops/posts/YYMMDD-릴리즈-vX.Y.md` Discord 공지 초안
8. [ ] 발행 후 post `status` → `live` + message ID를 change-log에 append
9. [ ] (자동) release-drafter가 다음 draft 릴리즈를 업데이트

---

## 5. 자동화

### release-drafter (GitHub Actions)

- 위치: `.github/workflows/release-drafter.yml`
- 설정: `.github/release-drafter.yml`
- 동작: main push 또는 PR merge 시 다음 릴리즈의 draft 노트를 자동 업데이트. PR 라벨(`feature`·`fix`·`chore`·`minor`·`patch` 등)로 카테고리·버전 자리수 결정.

### PR 템플릿

- 위치: `.github/pull_request_template.md`
- 영향 범위 체크박스와 Semver 분류를 강제로 생각하게 함.

---

## 6. CHANGELOG.md와 change-log.md 차이

| 파일 | 대상 | 성격 | 주기 |
|------|------|------|------|
| `CHANGELOG.md` (루트) | 외부·팀·감사 | 릴리즈 단위 이력 | 릴리즈마다 |
| `_system/agents/memory/change-log.md` | 에이전트·운영자 메모리 | 운영 변경 전반(릴리즈 외 변경 포함) | 수시 append |

둘은 **서로를 참조하되 중복 기재하지 않는다**. 릴리즈 발행 시 `CHANGELOG.md` 섹션은 change-log.md 해당 날짜 섹션을 요약본으로 옮긴다.
