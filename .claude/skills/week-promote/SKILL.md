---
name: week-promote
description: 주간 리뷰 직후 또는 시즌 마감 시 단기/중기 관찰을 장기기억으로 승격. weekly-review 완료 직후 실행 권장. 매 세션 후 routine 실행 불필요. "주간 승격", "장기기억 정리", "이번 주 패턴 기록해줘", "시즌 마무리" 등의 요청 시 사용.
---

# Week Promote — 장기기억 승격

단기 메모리(`change-log`, `active-context`)와 중기 메모리(`decisions/`)에서
**검증된 패턴과 중요 사항**을 장기기억(`long-term/`)으로 승격한다.

---

## 메모리 계층 구조

```
L1 단기  active-context.md      현재 상태 스냅샷 (덮어쓰기)
         change-log.md          변경 이력 (append)
              ↓
L2 중기  decisions/             구조적 결정 (개별 파일)
              ↓  (이 스킬)
L3 장기  long-term/
         ├── knowledge.md       검증된 운영 패턴·교훈 (append)
         ├── club-profile.md    클럽 특성·멤버 성향 (섹션 수정)
         └── seasons/           시즌별 요약 (YYSS-제목.md)
```

**세션 시작 시 읽기 순서**: `active-context.md` → `long-term/knowledge.md` → `long-term/club-profile.md` → `decisions/` (필요시만)

---

## 승격 매핑

| 소스 | 패턴 | 승격 목적지 |
|------|------|-----------|
| change-log / weekly-review | 2회+ 효과 확인된 운영 방법 | `MEMORY.md` — 운영 패턴 |
| change-log / weekly-review | 예상 밖 실패·교훈 (원인 명확) | `MEMORY.md` — 교훈 |
| decisions/ | 도구·플랫폼 비교 결정 | `MEMORY.md` — 도구 및 플랫폼 선택 근거 |
| 세션 기록 / weekly-review | 3회+ 반복 관찰된 멤버 패턴 | `MEMORY.md` — 멤버 성향 |
| 세션 기록 / weekly-review | 검증된 운영 선호 (세션 형식 등) | `MEMORY.md` — 운영 선호 |
| 시즌 마감 | 시즌 전체 회고 | `long-term/seasons/YYSS-시즌제목.md` |

---

## 워크플로우

### Step 1: 소스 파일 로드

```bash
ls -t 01_ops/reviews/*.md 2>/dev/null | head -3
ls _system/agents/memory/decisions/
head -50 _system/agents/memory/long-term/knowledge.md
```

### Step 2: 승격 후보 추출

소스 파일에서 키워드 스캔:

- **운영 패턴**: "효과적", "잘 됐다", "다음에도", "2회", "반복"
- **교훈**: "실패", "안 됐다", "다음에는", "예상 밖"
- **멤버 관찰**: 멤버 반응·참여도 관련 메모
- **결정 근거**: `decisions/`의 도구·플랫폼 관련 파일

### Step 3: 후보 제시 및 확인

```
승격 후보:

long-term/knowledge.md:
1. [ ] [패턴 제목] — [출처]
2. [ ] [교훈 제목] — [출처]

long-term/club-profile.md:
3. [ ] [관찰 내용] (N회 확인)

승격할 항목? (전체: all / 번호 선택 / 건너뛰기: skip)
```

**애매하면 승격하지 않는다.** 단기에 남아 있어도 다음 주 재검토 가능.

### Step 4: MEMORY.md 업데이트

`_system/agents/memory/MEMORY.md`의 해당 섹션에 append:

**운영 패턴·교훈·도구 근거**:
```markdown
### YYYY-MM-DD — [패턴명/교훈명]

[내용. 어떤 상황에서, 왜 효과적인가 / 왜 실패했는가.]

- **근거**: [세션 번호 또는 날짜]
- **적용 범위**: [어떤 상황에 해당하는가]
```

**멤버 성향·운영 선호** (해당 섹션 append):
```markdown
- YYYY-MM-DD | [관찰/선호 내용] (N회 확인, 근거: [세션/날짜])
```

### Step 5: 시즌 마감 — seasons/ 요약 생성

"시즌 마무리" 요청 또는 시즌 전환 시:

`_system/agents/memory/long-term/seasons/YYSS-시즌제목.md` 신규:

```markdown
---
title: 시즌N 회고
kind: log
area: system
status: live
created: YYYY-MM-DD
season: YYYY-SN
---

## 시즌 개요
- 기간: YYYY-MM-DD ~ YYYY-MM-DD
- 세션 수: N회 / 참가자: N명

## 주요 성과

## 핵심 교훈

## 다음 시즌으로 넘기는 것

## 승격 목록
- knowledge.md: [승격 패턴 목록]
- club-profile.md: [업데이트 항목]
```

### Step 6: change-log.md append

```markdown
## YYYY-MM-DD — 장기기억 승격
- knowledge.md: N개 항목 추가
- club-profile.md: [업데이트 내용]
```

---

## 판단 기준

| 유형 | 승격 OK | 승격 X |
|------|---------|--------|
| 운영 패턴 | 2회+ 효과 확인 | 1회 시도 |
| 교훈 | 실패 원인 명확 | 원인 불명 |
| 멤버 관찰 | 3회+ 반복 | 1~2회 관찰 |
| 결정 근거 | 대안 비교 있음 | 즉흥 선택 |

## 주의사항

- 이미 승격된 항목은 재승격하지 않는다 (knowledge.md 먼저 확인)
- `knowledge.md`는 **append 전용** — 기존 내용 수정 금지
- `club-profile.md`는 사실이 바뀐 경우에만 해당 줄 수정
- `decisions/` 파일은 이 스킬이 삭제하지 않는다 (이력 보존)
