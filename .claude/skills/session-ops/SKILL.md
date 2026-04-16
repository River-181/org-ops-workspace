---
name: session-ops
description: 세션 수명주기 4단계 통합 진입점. setup(폴더 생성) → launch(준비 점검) → record(기록 완성) → guard(에이전트 세션 관리). 서브커맨드 없이 /session-ops만 입력하면 현재 단계를 감지하여 권장 서브커맨드를 제안한다.
argument-hint: "[setup|launch|record|guard] [args]"
---

# session-ops

세션 수명주기의 4단계를 단일 진입점으로 통합한 스킬.

> **guard 주의**: `guard` 서브커맨드는 클럽 운영 세션(S{NN})이 아닌 **에이전트 대화 세션** 관리다.
> 클럽 세션 폴더 생성은 `setup`, 클럽 세션 종료 기록은 `record`를 사용한다.

---

## 서브커맨드

| 서브커맨드 | 원본 스킬 | 인자 | 동작 |
|-----------|----------|------|------|
| `setup` | session-setup | `[세션번호] [제목]` | 세션 폴더 + plan.md + record.md(빈) + materials/ 생성 |
| `launch` | session-launch | `[세션폴더] [D-N]` | D-N일 기준 준비 상태 점검 + 구간별 체크리스트 |
| `record` | session-record | `[세션폴더]` | 세션 종료 후 record.md 작성 + 폴더 정리 + 메모리 갱신 |
| `guard` | session-guard | (자연어 감지) | Claude 에이전트 대화 세션 시작·종료, 세션 ID, AAR, 메모리 연속성 |

---

## 서브커맨드 미지정 시 단계 감지

`/session-ops`만 입력되면 현재 세션 단계를 자동 감지하여 권장 서브커맨드를 제안한다:

```
판단 기준:
- 02_sessions/에 해당 번호 폴더 없음 → setup 권장
- plan.md 존재, 세션 날짜가 미래 → launch [D-N] 권장
- 세션 날짜가 오늘이거나 과거, record.md가 draft → record 권장
- "세션 시작"/"수고했어" 등 자연어 감지 → guard 자동 적용
```

---

## /session-ops setup [세션번호] [제목]

### 목적

새 세션의 폴더 구조와 초기 문서를 자동 생성한다.

### 입력

- **세션 번호**: NN (예: 04)
- **세션 제목**: 제목 (예: 디지털정리법)

인자가 없으면 사용자에게 번호와 제목을 물어본다.

### 절차

#### 1. 중복 확인
`02_sessions/`에서 동일 번호의 세션 폴더가 있는지 확인한다.
있으면 사용자에게 알리고 중단한다.

#### 2. 폴더 생성

```
02_sessions/S{NN}-{제목슬러그}/
├── S{NN}-{제목슬러그}-plan.md
├── S{NN}-{제목슬러그}-record.md
└── materials/
    └── .gitkeep
```

> **파일명은 폴더명과 동일한 슬러그를 접두사로 붙인다.** (예: 폴더 `S04-디지털정리법/` → 파일 `S04-디지털정리법-plan.md`)
> Quick Switcher·Recent Files에서 세션 번호+주제 모두 보여 구분이 가능하다.

#### 3. S{NN}-{제목슬러그}-plan.md 작성
`01_ops/templates/tpl-세션기획.md` 복사 후 frontmatter 채우기:

```yaml
---
title: "S{NN} 세션 기획안 — {제목}"
kind: session-plan
area: sessions
status: draft
created: {오늘 날짜}
session_id: "S{NN}"
up: "[[MOC-세션]]"
tags:
  - session/S{NN}
  - session/plan
---
```

> plan 파일에는 `participants:` 필드를 넣지 않는다.
> 실제 참석자는 세션 종료 후 record 파일에 기록한다.

#### 4. S{NN}-{제목슬러그}-record.md 작성
`01_ops/templates/tpl-세션기록.md` 복사 후 frontmatter 기본값만 채우기:

```yaml
---
title: "S{NN} 세션 기록 — {제목}"
kind: session-record
area: sessions
status: draft
created: {오늘 날짜}
session_id: "S{NN}"
up: "[[MOC-세션]]"
plan: "[[S{NN}-{제목슬러그}-plan]]"
participants: []
materials: []
tags:
  - session/S{NN}
  - session/record
---
```

> 본문은 비워두고, 세션 종료 후 `session-ops record` 서브커맨드로 채운다.

#### 5-a. Obsidian CLI로 파일 title 설정

```bash
obsidian vault="[볼트명]" property:set name="title" \
  value="S{NN} 세션 기획안 — {제목}" \
  path="02_sessions/S{NN}-{제목슬러그}/S{NN}-{제목슬러그}-plan.md" silent

obsidian vault="[볼트명]" property:set name="title" \
  value="S{NN} 세션 기록 — {제목}" \
  path="02_sessions/S{NN}-{제목슬러그}/S{NN}-{제목슬러그}-record.md" silent
```

#### 5. backlog 연결
`02_sessions/backlog/`에서 관련 아이디어가 있으면
`S{NN}-plan.md`의 참고 자료에 `[[wikilink]]`로 연결한다.

#### 6. 확인 메시지
생성된 파일 목록을 사용자에게 보여준다.

### 세션 수명주기

```
session-ops setup (기획 전)
  → S{NN}-{슬러그}-plan.md 작성/수정 (@prep 에이전트)
  → slide-prep (슬라이드 외주 준비)
  → session-ops launch (D-3 점검)
  → 세션 진행
  → session-ops record (종료 후)
  → S{NN}-{슬러그}-record.md: participants, 자료, 피드백, 회고
```

### 주의사항

- 세션 폴더명에 공백 금지 — 하이픈 사용
- materials/는 빈 폴더로 생성 (git 추적: `.gitkeep` 추가)
- 파일명은 반드시 `S{NN}-{제목슬러그}-plan.md` 형식 — 번호만 있는 `S{NN}-plan.md` 금지
- 파일 이름 변경 시 반드시 `obsidian rename` 사용 — wikilink 자동 업데이트

---

## /session-ops launch [세션폴더] [D-N]

### 목적

세션 당일 N일 전 시점에서 준비 상태를 전수 점검하고,
남은 기간별 액션 아이템을 정리하여 누락 없이 세션에 임할 수 있도록 한다.

### 입력

- **세션 폴더**: `02_sessions/S{NN}-{제목}/`
- **D-N**: 며칠 전인지 (예: 3 → D-3 체크리스트 생성)

인자가 없으면 사용자에게 묻는다.

### 절차

#### 1. 세션 컨텍스트 파악

- `plan.md` 읽기: 일시, 장소, 진행자, 아젠다, 준비물
- `materials/` 폴더 스캔: 파일 목록 + 각 파일 status 확인

#### 2. 발행물 상태 확인

| 항목 | 확인 방법 |
|------|---------|
| 모집 공고 | `01_ops/recruit/` + `04_studio/posts/`에서 status: live 여부 |
| 참가 폼 링크 | plan.md 또는 슬라이드 파일에서 실제 URL 존재 여부 |
| Discord 공지 | `01_ops/recruit/` 또는 plan.md 발행 섹션 확인 |

#### 3. 자료 완성 상태 확인

슬라이드·스크립트·핸드아웃 각 파일에 대해:
- frontmatter `status:` 값 확인 (draft / live)
- `[링크]`, `TBD` 등 미입력 플레이스홀더 검색
- 외주/위임 작업 완료 여부 (키노트 제작, AI 다이어그램, 이미지 수집)

#### 4. D-N 구간별 체크리스트 생성

**D-7 이상 (자료 제작 단계)**
- [ ] 슬라이드 MD 콘텐츠 완성 (`slide-prep` 스킬)
- [ ] 스크립트 초안 완성
- [ ] 모집 공고 발행
- [ ] 외주 키노트 제작 의뢰

**D-3 ~ D-5 (외주·위임 완료 단계)**
- [ ] 키노트/PPT 완성본 수령 및 검토
- [ ] 다이어그램·이미지 수집 완료
- [ ] 스크립트 리허설 1회

**D-1 ~ D-2 (최종 점검 단계)**
- [ ] 슬라이드 최종 PDF 저장 (예비본)
- [ ] 보조 툴 테스트 (FigJam, Miro 등)
- [ ] 장소 최종 확인 (프로젝터, 음향, 인터넷)
- [ ] Discord 당일 공지 초안 작성
- [ ] 참가자 명단 확인

**D-0 (당일)**
- [ ] 장비 점검 (노트북 충전, HDMI 어댑터)
- [ ] 슬라이드 발표 모드 테스트
- [ ] 체크인 명단 준비
- [ ] 진행자 간 역할 최종 확인

#### 5. 미완 항목 우선순위 보고

| 항목 | 담당 | 마감 | 메모 |
|------|------|------|------|
| ... | ... | ... | ... |

#### 6. 선택: Discord 공지 초안 작성

D-1 시점 또는 요청 시, 당일 참가자 대상 Discord 공지 초안 생성:
- 세션 일시·장소
- 준비물 (있다면)
- 입장 안내
- 진행자 연락처

### 사용 시점

| 언제 | 목적 |
|------|------|
| D-7 | 외주 의뢰 전 자료 완성도 확인 |
| D-3 | 외주 수령 후 최종 점검 |
| D-1 | 당일 대비 최종 체크 |

---

## /session-ops record [세션폴더]

### 목적

세션이 끝난 뒤 **3단계 마무리**를 한 번에 완료한다:

1. `S{NN}-{슬러그}-record.md` 내용 채우기
2. 세션 폴더 파일 정리 (이름, title property, status)
3. 에이전트 메모리 갱신 (active-context, change-log)

### record.md vs plan.md

| | S{NN}-{슬러그}-plan.md | S{NN}-{슬러그}-record.md |
|-|---------|-----------|
| 성격 | 설계·의도 | 실제·결과 |
| participants | ✗ | ✓ (실제 참석자) |
| 자료 링크 | 초안 | 실제 사용된 것 |
| 피드백 | ✗ | ✓ |
| 회고 | ✗ | ✓ |

### 절차

#### 1. 세션 폴더 파악

기대 구조:
```
S{NN}-{슬러그}/
├── S{NN}-{슬러그}-plan.md
├── S{NN}-{슬러그}-record.md
└── materials/
```

`S{NN}-{슬러그}-record.md`가 없으면 `01_ops/templates/tpl-세션기록.md` 복사해 생성.

#### 2. Frontmatter 채우기

```yaml
---
title: "S{NN} 세션 기록 — {제목}"
kind: session-record
area: sessions
status: live
created: {세션 날짜}
session_id: "S{NN}"
up: "[[MOC-세션]]"
plan: "[[S{NN}-{슬러그}-plan]]"
participants:
  - "[[멤버1]]"
materials:
  - "[[materials/슬라이드]]"
tags:
  - session/S{NN}
  - session/record
---
```

> **participants는 record 파일에만 존재한다.**
> `plan:` 링크는 `"[[S{NN}-{슬러그}-plan]]"` — 번호+슬러그 모두 포함.

#### 3. 본문 섹션 채우기

- **발표 / 자료**: Drive 링크, materials/ wikilink, 외부 URL
- **실제 진행 내용**: plan 대비 변경 항목 "(계획 대비 변경)" 표시
- **피드백**: 수집 내용 정리. 없으면 "수집 안 됨"
- **회고**: 수치 지표 테이블 반드시 채우기

#### 4. 파일 정리

```bash
# plan 파일 archived 처리
obsidian vault="[볼트명]" property:set name="status" value="archived" \
  path="02_sessions/S{NN}-{슬러그}/S{NN}-{슬러그}-plan.md" silent
```

#### 5. 에이전트 메모리 갱신

```markdown
# change-log.md append
## YYYY-MM-DD

- S{NN} 세션 완료
  - 참가자: {N}명
  - 주요 내용: {한 줄 요약}
```

active-context.md: 세션 상태 완료 반영 + **전체 덮어쓰기**.

> **중복 실행 주의**: `record` 실행 후 `memory-update`를 추가 실행하면 change-log에 중복 항목 발생.
> 클럽 세션 종료 시에는 `record`만 실행하면 충분하다.

### 완성 기준

- [ ] frontmatter: title, participants, plan, session_id, materials, status: live
- [ ] `plan:` 링크 형태 `"[[S{NN}-{슬러그}-plan]]"` (번호+슬러그 모두 포함)
- [ ] 발표 자료 링크 모두 기록
- [ ] 피드백 섹션 (없으면 "수집 안 됨")
- [ ] 회고 + 수치 지표
- [ ] plan status → archived
- [ ] change-log 날짜 섹션 추가
- [ ] active-context 갱신

---

## /session-ops guard

> **주의**: 클럽 운영 세션(S{NN}) 관리가 아닌 **에이전트 대화 세션** 관리다.
> 클럽 세션 폴더 생성 → `setup`, 클럽 세션 종료 기록 → `record`.

### 목적

에이전트 대화 세션의 시작과 끝을 관리하여 메모리의 연속성을 유지한다.

자연어 감지: "세션 시작", "작업 시작", "오늘 할 일", "세션 끝", "수고했어", "종료"

### 1. 세션 시작

#### 세션 ID 생성

```
[클럽식별자]-YYMMDD-GGG-NN
```

- `[클럽식별자]`: `_system/identity/identity.md`의 slug 값
- `YYMMDD`: 오늘 날짜
- `GGG`: 목표 핵심 명사 약자 (예: SESSION-PREP, TAG-CLEAN)
- `NN`: 당일 일련번호 (01부터)

#### 시작 선언

```
세션 ID: [클럽식별자]-260417-SESSION-PREP-01
목표: [이번 세션에서 달성할 것 — 정량적 1문장]
```

시작 직후 읽기 순서:
1. `_system/agents/memory/active-context.md`
2. `_system/agents/memory/MEMORY.md`

### 2. 세션 종료

"수고했어", "끝내자", "종료" 등 감지 시 자동 실행.

#### AAR 3줄 요약

1. **Changes**: 변경·생성된 파일 및 결정 (경로 포함)
2. **Deliverables**: 주요 산출물
3. **Next Action**: 다음 세션 첫 작업 1가지

#### 메모리 갱신

**active-context.md 덮어쓰기**:
```markdown
# 현재 상태 (YYYY-MM-DD 갱신)

## 클럽 현황
[한 줄]

## 진행 중인 작업
- [ ] 작업1 (담당자, 마감)

## 우선순위
1.

## 블로커
(없으면 생략)

## 다음 세션
[1~2줄]
```

**change-log.md append**:
```markdown
## YYYY-MM-DD | [클럽식별자]-YYMMDD-GGG-NN

- **변경**: [파일/시스템 변경 목록]
- **결정**: [이번 세션에서 내린 결정]
- **다음**: [다음 세션 첫 작업]
```

구조적 결정 → `update-governance` 스킬로 위임.

### 3. 적용 규칙

- 세션 종료 감지 시 사용자 요청 없어도 AAR 자동 제공
- active-context.md: **전체 교체** (append 금지)
- change-log.md: **append 전용** (덮어쓰기 금지)
