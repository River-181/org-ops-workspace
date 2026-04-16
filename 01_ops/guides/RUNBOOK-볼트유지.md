---
title: RUNBOOK — 볼트 유지
kind: guide
area: ops
status: live
created: 2026-04-16
tags:
  - ops/guide
  - ops/runbook
up: "[[MOC-운영]]"
---

# RUNBOOK — 볼트 유지

> 운영팀이 주 1회 30분 안에 볼트 건강성을 점검·유지하기 위한 최소 절차서.
> `01_ops/guides/` 폴더의 다른 가이드와 달리 이 문서는 **매주 반복 실행되는 체크리스트**다.

---

## 1. 목적

책임자가 자리를 비워도 볼트가 건강하게 돌아가야 한다. 이 RUNBOOK은 운영팀 중 한 명이 월요일 오전 30분을 투자해 볼트 상태를 파악·정리하고, 다가오는 세션 준비와 인박스 잔존 파일을 점검할 수 있도록 만들어졌다. 특별한 기술 지식 없이 여기 적힌 체크리스트와 스킬 이름만 알면 된다.

---

## 2. 주간 체크리스트 (월요일 AM 30분)

> 순서대로 진행. 각 항목은 2~5분.

- [ ] `_system/agents/memory/active-context.md` 읽어 지난 주 주요 이벤트 파악
- [ ] `_system/agents/memory/change-log.md` 최신 섹션 훑기 (상단 1~2개 날짜)
- [ ] `/weekly-review` 스킬 실행 → 결과 `01_ops/reviews/YYMMDD-주간리뷰.md`로 저장
- [ ] `06_inbox/captures/`, `06_inbox/triage/` 잔존 파일 확인 → 필요 시 `/inbox-triage` 실행
- [ ] 다가오는 세션(`02_sessions/`) 준비 상태 점검
  - D-7 이내면 `/session-ops launch` 실행
  - D-21 이내이고 폴더 없으면 `/session-ops setup` 실행
- [ ] 장기기억 승격 후보 검토 → `/week-promote`

### 30분을 초과할 때

- 체크리스트를 끊고 다음 주로 이월할 항목을 주간리뷰에 "이월" 섹션으로 명시
- 긴급하지 않은 한 CRITICAL만 처리하고 WARNING은 다음 주로 넘김

---

## 3. 건강성 진단 — 월 1회

> 매월 첫 번째 주간 체크리스트에 붙여 실행. 10~15분 추가.

- [ ] `/verify-implementation` 실행 — 통합 검증 보고서 생성 (권장)
  - 내부적으로 `vault-health`, `link-audit`, `tag-audit`, `frontmatter-scan`을 순차 실행
- [ ] 또는 개별 실행: `/vault-health`, `/link-audit`, `/tag-audit`, `/frontmatter-scan`
- [ ] 결과 파일을 `_system/obsidian/audits/YYMMDD-*.md` 형식으로 저장
- [ ] 보고서 읽고 항목 분류:
  - **CRITICAL** → 당주 내 해결 (frontmatter 누락, 깨진 링크, 고아 MOC 등)
  - **WARNING** → 다음 주 체크리스트에 포함
  - **INFO** → 참고만

### 감사 결과 저장 규칙

```
_system/obsidian/audits/
├── 볼트-감사.md            ← 상시 유지
├── 라이브러리-감사.md       ← 상시 유지
└── YYMMDD-*.md             ← 월별 스냅샷 (예: YYMMDD-verify.md)
```

---

## 4. 세션 실행 주기

매 세션마다 아래 스킬을 순서대로 호출한다. 세션 담당자가 없을 때 RUNBOOK 실행자가 대리 호출한다.

| 시점 | 스킬 | 역할 |
|------|------|------|
| D-21 | `/session-ops setup` | 세션 폴더 + plan + record 초안 자동 생성 |
| D-7 | `/slide-prep` | 슬라이드.md → 디자인 스펙 + 이미지 가이드 |
| D-3 | `/session-ops launch` | 준비 상태 종합 점검 체크리스트 |
| D+1 | `/session-ops record` | record.md 완성 + 파일 정리 + 메모리 갱신 |

### 회고 포인트 (세션 직후)

세션이 끝나면 `_system/agents/memory/long-term/club-profile.md`에 멤버 성향 관찰 **1건 append**. 한 줄이어도 된다.

이 습관이 장기적으로 클럽 프로필을 풍부하게 만든다.

---

## 5. 릴리즈 컷오프 기준

`.claude/` 설정·스킬·에이전트에 변경이 누적되면 릴리즈한다.

### 언제 릴리즈하는가

- 기능 3~5개 누적 → `/release` 호출
- 마이너 버그 픽스 1~2개 → 다음 릴리즈에 모아서
- 스킬 신규 추가 → 기능 카운트에 포함
- `_system/rules.md`나 `CLAUDE.md` 구조 변경 → 단독 릴리즈도 가능

### 버전 규칙

```
v0.X      ← 마이너 (새 기능, 스킬 추가)
v0.X.Y    ← 패치 (버그 픽스, 문서 수정)
v1.0      ← 시즌 전환 등 중대 마일스톤
```

`/release` 스킬이 AI 초안을 제안 → 승인 → git push + GitHub release + Discord 공지를 일괄 처리한다.

---

## 6. 장애·이슈 대응

### 자주 마주치는 문제

| 증상 | 원인 | 대응 |
|------|------|------|
| Icon 파일 재발 | macOS Finder가 생성 | `find . -name "Icon*" -delete` 후 `/clean-sweep` 실행 |
| `active-context.md` 200줄 초과 | 덮어쓰기 누락으로 누적 | 3주 이상 된 섹션을 `change-log.md`로 이관 후 요약만 남김 |
| frontmatter 필수 필드 누락 | 파일 생성 시 템플릿 미사용 | `/frontmatter-scan`으로 목록 확인 → 해당 파일 수동 보정 |
| 고아 파일 발생 | `up:` 링크 누락 | `/link-audit` → 보고서의 고아 목록에 `up:` 추가 |
| `.DS_Store` 재발 | macOS 기본 동작 | `/clean-sweep` 주기 실행, `.gitignore` 유지 확인 |

### 복구 불능 상황

- git log에서 마지막 정상 커밋 찾기 (`git log --oneline -20`)
- 해당 커밋 해시로 특정 파일만 복원: `git checkout <hash> -- <path>`
- 볼트 전체를 롤백하지는 않는다 (다른 작업 손실 위험)

---

## 7. 연락·인수인계

### 연락 채널

| 상황 | 채널 |
|------|------|
| 일반 질문 | Discord ops 스레드 |
| 긴급 | [관리자명] 직접 DM |
| 복잡한 구조 변경 | `/update-governance` 스킬 + `_system/agents/memory/decisions/`에 결정 로그 |

### 인수인계 규칙

- 주간 체크리스트 결과는 `01_ops/reviews/YYMMDD-주간리뷰.md`에 저장되므로 다음 실행자가 읽으면 연속성 확보
- 본인이 처리하지 못한 항목은 반드시 "이월" 또는 "미결" 섹션으로 명시
- 의사결정이 필요한 사항은 Discord ops 스레드에 올리고 [관리자명]의 회신 기다림

### 실행자 간 교대

- 주 1회 실행자가 바뀔 때 → Discord ops 스레드에 "다음 주 @이름" 형태로 명시
- 연속 2주 동일 실행자 → 본인 판단으로 처리 가능
- 4주 이상 실행자가 없으면 → [관리자명]에게 상황 보고

---

## 8. 자주 쓰는 스킬 맵

> 전체 스킬 목록은 `CLAUDE.md`의 Skills 섹션 또는 `.claude/skills/MAP-스킬.md` 참조.

| 상황 | 스킬 | 주기 |
|------|------|------|
| 주간 현황 파악 | `/weekly-review` | 주 1회 |
| 인박스 분류 | `/inbox-triage` | 주 1회 |
| 통합 검증 | `/verify-implementation` | 월 1회 |
| 볼트 위생 | `/vault-health` | 월 1회 (또는 이슈 시) |
| 링크 진단 | `/link-audit` | 월 1회 |
| 태그 진단 | `/tag-audit` | 월 1회 |
| frontmatter 진단 | `/frontmatter-scan` | 월 1회 |
| 세션 폴더 생성 | `/session-ops setup` | 세션별 D-21 |
| 슬라이드 가공 | `/slide-prep` | 세션별 D-7 |
| 세션 직전 점검 | `/session-ops launch` | 세션별 D-3 |
| 세션 마무리 | `/session-ops record` | 세션별 D+1 |
| 위생 정리 | `/clean-sweep` | 필요 시 |
| 기억 승격 | `/week-promote` | 주 1회 |
| 메모리 갱신 | `/memory-update` | 필요 시 |
| 릴리즈 | `/release` | 기능 3~5개 누적 시 |
| Discord 공지 | `/discord-announce` | 필요 시 |
| 거버넌스 업데이트 | `/update-governance` | 구조 변경 시 |

---

## 관련 문서

- [[MOC-운영]] — 운영 영역 전체 허브
- [[_system/rules.md]] — 볼트 운영 규칙
- [[온보딩-Lv1-Day1]] — 신규 운영팀원 첫날 가이드
- [[온보딩-Lv2-Week1]] — 주간 루틴 첫 실행 가이드
- [[온보딩-Lv3-Month1]] — 세션 사이클 완주 가이드
