---
name: session-guard
description: 클럽 운영 세션의 시작과 종료를 관리합니다. 세션 ID 생성, 목표 선언, 종료 시 AAR(After Action Report) 자동 작성 + 메모리 갱신. "세션 시작", "작업 시작", "오늘 할 일", "세션 끝", "수고했어", "종료" 등의 맥락에서 자동 적용.
---

# Session Guard

[볼트명] 세션의 시작과 끝을 관리하여 에이전트 메모리의 연속성을 유지하는 스킬.

---

## 1. 세션 시작 (Session Open)

세션 시작 또는 첫 작업 요청 수신 시 활성화.

### 세션 ID 생성

```
[클럽명]-YYMMDD-GGG-NN
```

- `[클럽명]`: 프로젝트 식별자 (고정)
- `YYMMDD`: 오늘 날짜 (예: 260417)
- `GGG`: 이번 세션 목표의 핵심 명사 약자 (예: SESSION-PREP, TAG-CLEAN, WEEKLY-REV)
- `NN`: 당일 세션 일련번호 (01부터)

### 시작 선언 출력

```
세션 ID: [클럽명]-260417-SESSION-PREP-01
목표: [이번 세션에서 달성할 것 — 정량적 1문장]
```

시작 직후 다음 순서로 읽어 컨텍스트를 파악한다:
1. `_system/agents/memory/active-context.md` — 이전 세션 상태 (없으면 초기 상태로 간주)
2. `_system/agents/memory/MEMORY.md` — 검증된 운영 패턴·교훈 (장기기억)

---

## 2. 세션 종료 (Session Close)

"수고했어", "끝내자", "종료", "마무리" 등으로 세션 종료가 감지되면 자동 실행.
사용자 요청이 없어도 먼저 AAR을 제공한다.

### AAR (After Action Report) 3줄 요약

1. **Changes**: 이번 세션에서 실제 변경·생성된 파일 및 결정 (파일 경로 포함)
2. **Deliverables**: 주요 산출물 (세션 기획안, 기록, 발행물 등)
3. **Next Action**: 다음 세션에서 이어야 할 가장 시급한 작업 1가지

### 메모리 갱신

#### active-context.md 덮어쓰기

`_system/agents/memory/active-context.md`를 현재 상태로 교체:

```markdown
# 현재 상태 (YYYY-MM-DD 갱신)

## 클럽 현황
[클럽 현재 상태 한 줄]

## 진행 중인 작업
- [ ] 작업1 (담당자, 마감)
- [ ] 작업2

## 우선순위
1.
2.

## 블로커
(없으면 생략)

## 다음 세션
[다음에 할 일 1~2줄]
```

#### change-log.md append

`_system/agents/memory/change-log.md` 하단에 섹션 추가:

```markdown
## YYYY-MM-DD | [클럽명]-YYMMDD-GGG-NN

- **변경**: [파일/시스템 변경 목록]
- **결정**: [이번 세션에서 내린 결정]
- **다음**: [다음 세션 첫 작업]
```

#### decisions/ 기록 (해당 시)

구조적 결정이 있었으면 `update-governance` 스킬로 위임한다.

---

## 3. 적용 규칙

- 세션 종료 감지 시 사용자 요청 없어도 AAR 자동 제공
- 세션 기획안·기록 등 산출물 frontmatter에 `session_id: [클럽명]-...` 포함 권장
- active-context.md 갱신은 **전체 교체** — 이전 내용을 append하지 않는다
- change-log.md는 **append 전용** — 기존 내용을 덮어쓰지 않는다
