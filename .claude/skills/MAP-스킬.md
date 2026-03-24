---
title: 스킬 맵
kind: map
status: live
created: 2026-03-24
---

# 스킬 맵

> 모든 스킬의 인덱스. 스킬 추가 시 이 파일과 `CLAUDE.md`를 업데이트한다.
> 각 스킬 정의: `.claude/skills/{스킬명}/SKILL.md`

---

## 스킬 목록

### 세션 운영

| 스킬 | 호출 | 설명 |
|------|------|------|
| session-setup | `/session-setup` | 세션 폴더 + 기획안 + 체크리스트 자동 생성 |
| session-launch | `/session-launch` | D-N일 기준 준비 상태 점검 + 당일 체크리스트 |
| session-record | `/session-record` | 세션 종료 후 record 완성 — 자료·피드백·회고 |
| weekly-review | `/weekly-review` | 볼트 전체 스캔 → 주간 현황 보고 생성 |

### 볼트 건강

| 스킬 | 호출 | 설명 |
|------|------|------|
| vault-health | `/vault-health` | 네이밍·메타데이터·링크·깊이 위생 점검 |
| link-audit | `/link-audit` | 고아 파일, 단절 군집, up 누락 진단 |
| frontmatter-scan | `/frontmatter-scan` | frontmatter 완성도 통계 — 필드별·폴더별 |
| tag-audit | `/tag-audit` | 태그 일관성 — 미등록·네임스페이스 위반 |
| manifest-check | `/manifest-check` | 볼트설정 매니페스트 ↔ .obsidian/ 비교 |
| clean-sweep | `/clean-sweep` | Icon, 빈 폴더, .DS_Store, 중복 탐지 및 삭제 제안 |

### 지식 관리

| 스킬 | 호출 | 설명 |
|------|------|------|
| reference-save | `/reference-save` | 조사 결과 저장 + `_system/reference/` 인덱스 갱신 |
| reference-lookup | `/reference-lookup` | 저장된 레퍼런스 검색 및 반환 |
| memory-update | `/memory-update` | 에이전트 메모리(active-context, change-log) 갱신 |
| progress-update | `/progress-update` | 활성 프로젝트 PROGRESS.md 갱신 |
| inbox-triage | `/inbox-triage` | `06_inbox/` 파일 분류 및 라우팅 제안 |

### 콘텐츠 & 멤버

| 스킬 | 호출 | 설명 |
|------|------|------|
| slide-prep | `/slide-prep` | 슬라이드 MD → 외주 전달용 완성본 |
| member-onboard | `/member-onboard` | 새 멤버 운영 DB 파일 생성 |

### 온보딩

| 스킬 | 호출 | 설명 |
|------|------|------|
| org-init | `/org-init` | 새 조직 온보딩 — 6파일 identity 구조 초기화 |

---

## 스킬 추가 방법

1. `.claude/skills/{스킬명}/` 폴더 생성
2. `SKILL.md` 작성 — frontmatter: `name`, `description`, `allowed-tools`
3. 위 테이블에 행 추가 (적절한 분류에)
4. `CLAUDE.md` 스킬 섹션 테이블 업데이트
5. 외부 도구 연동 스킬이라면 `_system/tools/README.md`도 업데이트

---

## 참조

- 에이전트 맵: `_system/obsidian/maps/MAP-에이전트.md`
- 도구 맵: `_system/obsidian/maps/MAP-도구.md`
- CLAUDE.md 스킬 섹션
