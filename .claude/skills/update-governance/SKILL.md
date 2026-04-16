---
name: update-governance
description: CLAUDE.md 스킬 테이블, 클럽타임라인, 라우팅 목적지가 불명확한 경우의 거버넌스 파일 갱신 (특수). routine한 세션 결과 기록은 memory-update를 사용한다. "스킬 추가했어", "시스템 바꿨어", "이 결정 어디 써야 해" 등 라우팅 판단이 필요할 때 사용.
---

# Update Governance

워크스페이스 거버넌스 파일의 변경사항을 올바른 위치로 라우팅·기록하는 메타 스킬.

> `memory-update` 스킬과의 분업:
> - `memory-update` — 세션 작업 결과를 메모리에 반영하는 **일반 절차**
> - `update-governance` — "이 변경을 어디에 써야 하나"가 불명확할 때 **라우팅 판단 + 최소 diff 기록**

---

## 라우팅 매트릭스

| 변경 유형 | 목적지 | 방식 |
|----------|--------|------|
| 현재 클럽 상태 변화 (세션 완료, 프로젝트 착수 등) | `_system/agents/memory/active-context.md` | **덮어쓰기** |
| 파일·시스템·스킬 변경 이력 | `_system/agents/memory/change-log.md` | **append** |
| 대안 비교 후 구조적 결정 | `_system/agents/memory/decisions/YYMMDD-결정제목.md` | **신규 파일** |
| 클럽 역사에 남길 마일스톤 | `01_ops/strategy/클럽타임라인.md` | **append** |
| 스킬·에이전트 추가/변경 | `CLAUDE.md` 스킬/에이전트 테이블 | **행 추가·수정** |
| 볼트 전체 규칙 변경 | `CLAUDE.md` 또는 `_system/rules.md` | **섹션 내 수정** |

---

## Workflow

### Step 1: 변경사항 분류

입력 또는 세션 맥락에서 변경의 **종류**와 **영구성**을 판단한다:

```
- 일회성 작업 완료? → change-log.md append만
- 클럽 운영 상태 바뀜? → active-context.md 덮어쓰기
- 되돌리기 어려운 구조 결정? → decisions/ 신규 파일
- 역사적 이벤트 (첫 세션, 시즌 전환 등)? → 클럽타임라인.md append
- 스킬/에이전트 추가·삭제? → CLAUDE.md 테이블 갱신
```

### Step 2: 목적지 제시 (확인 요청)

사용자에게 라우팅 결과를 한 줄씩 제시:

```
이 변경을 다음 위치에 기록합니다:
  1. _system/agents/memory/active-context.md — [내용 한 줄]
  2. _system/agents/memory/decisions/YYMMDD-결정제목.md — [결정 요약]
진행? (y / 수정)
```

### Step 3: 목적지 파일 업데이트

각 목적지에 Edit/Write 적용. 수정 전 해당 파일을 읽어 중복·충돌 확인.

**active-context.md** (덮어쓰기):
```markdown
# 현재 상태 (YYYY-MM-DD 갱신)

## 클럽 현황
[한 줄]

## 진행 중인 작업
- [ ] 작업명 (담당, 마감)

## 우선순위
1.

## 블로커
(없으면 생략)

## 다음 세션
[1~2줄]
```

**change-log.md** (append):
```markdown
## YYYY-MM-DD
- [변경 내용 — 파일 경로 포함]
- 이유: [한 줄]
```

**decisions/YYMMDD-결정제목.md** (신규):
```markdown
---
title: 결정 제목
status: live
created: YYYY-MM-DD
---

## 결정
[한 문장]

## 맥락
[왜 이 결정을 했는가]

## 대안
[고려했지만 선택하지 않은 것과 이유]

## 후속 조치
[이 결정으로 해야 할 일]
```

**클럽타임라인.md** (append):
```markdown
| YYYY-MM-DD | [이벤트] | [의미·맥락] |
```

**CLAUDE.md 스킬 테이블** (행 추가):
```markdown
| `스킬명` | 설명 |
```

### Step 4: 완료 확인

갱신된 파일 목록과 변경 요약을 출력한다.

---

## 중복 방지 원칙

- 같은 사실을 여러 파일에 복제하지 않는다
  - 결정 상세 → decisions/ 파일만. change-log는 파일명 참조만
  - 클럽 현황 상세 → active-context.md만. change-log는 이벤트만
- 수정 전 반드시 해당 파일을 읽어 동일 내용이 이미 있는지 확인
- 사용자가 파일을 직접 지정했으면 그 판단 우선

---

## 주의사항

- `memory-update`가 이미 처리한 내용은 중복 갱신하지 않는다
- decisions/ 파일은 **대안 비교가 있는 구조적 결정**만. 일상적 변경은 change-log로 충분
- 클럽타임라인.md는 클럽 연혁에 남을 이벤트만 (세션 완료, 시즌 전환, 주요 결정 등)
