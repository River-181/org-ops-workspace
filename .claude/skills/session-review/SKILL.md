---
name: session-review
description: 세션 기록 완료 후 운영팀 회고 + 다음 세션 연결 — ops 리뷰 초안 자동 생성
argument-hint: "[세션ID] (예: S01)"
allowed-tools: Read, Edit, Write, Bash
---

# session-review

## 목적

세션 record.md가 완성된 뒤, 운영팀 회고 초안을 작성하고 다음 세션으로 이어지는 힌트를 추출한다.
리뷰 파일은 `01_ops/posts/`에 저장하며, 발행은 `discord-announce`로 별도 진행한다.

---

## 최소 선행 조건

| 조건 | 확인 방법 |
|------|----------|
| `record.md` 존재 | `02_sessions/S{NN}-*/S{NN}-*-record.md` |
| 출석·결과·액션아이템 기록 완료 | record.md 내 **피드백** + **액션 아이템** 섹션 채워짐 |
| record.md `status: live` | frontmatter 확인 |

record.md가 `draft` 상태면 먼저 `/session-ops record` 스킬로 완성한다.

---

## 절차

### 1. 입력 파악

인자로 세션 ID(`S{NN}`)가 주어지지 않으면 사용자에게 확인한다.

```
세션 ID를 알려주세요 (예: S01).
```

세션 ID가 확인되면:

```bash
ls 02_sessions/ | grep "^S{NN}-"
```

폴더를 특정한 뒤 아래 파일을 읽는다:

```
02_sessions/S{NN}-{슬러그}/S{NN}-{슬러그}-plan.md    ← 목표·설계
02_sessions/S{NN}-{슬러그}/S{NN}-{슬러그}-record.md  ← 실제·결과
```

---

### 2. 목표 달성 점검

plan.md의 **세션 목표** 섹션과 record.md의 **실제 진행 내용** 섹션을 비교한다.

| 점검 항목 | 근거 필드 |
|----------|----------|
| 목표 달성 여부 (Y/N/부분) | plan 목표 vs record 진행 내용 |
| 계획 대비 변경 사항 | record "계획 대비 변경" 표시 |
| 미완료 항목 | record 액션 아이템 중 이월 항목 |

---

### 3. 회고 정리

record.md의 **피드백** · **회고** 섹션에서 아래 3가지를 추출한다:

- **잘된 것** — record "잘된 점" 섹션 요약 (2~3개)
- **아쉬운 것** — record "개선할 점" 섹션 요약 (1~2개)
- **멤버 반응** — record "수치 지표" 중 만족도·분위기 값 포함

---

### 4. ops 리뷰 초안 작성

#### 발행문 포맷 (200~400자)

```
🔧 **S{N} 세션 리뷰 — YYYY-MM-DD**

[1~2줄 세션 요약]

**잘됐던 것**
- ...
- ...

**개선할 것**
- ...

**다음 세션 힌트**
- ...
```

작성 규칙:
- Discord markdown 사용 (`**굵게**`, `` `코드` ``)
- 이모지는 헤더에만 1개
- 멤버 실명 금지 — "참가자", "운영팀" 등 일반 표현 사용
- 한국어 기본, 기술 용어는 영어 유지

---

### 5. 다음 세션 힌트 추출

record.md의 **"다음에 다루면 좋을 주제"** 및 **"다음에 바꿀 것"** 섹션에서 1~3개 추출.

추출 기준:
- 멤버 반응이 높았던 주제
- 미완료 액션 아이템 중 다음 세션 반영 가능한 것
- 운영 개선 사항 (진행 방식, 준비물, 시간 배분)

---

### 6. Drop 가능성 메모

세션 내용 중 아래 기준에 해당하면 Drop 후보로 메모한다:

| 기준 | 예시 |
|------|------|
| 멤버 반응 특히 좋았던 활동·아이디어 | 아이스브레이킹 방식, 시각화 도구 |
| 재현 가능한 방법론·템플릿 | 폼 구성, 슬라이드 구조 |
| 외부 공유 가치 있는 인사이트 | 클럽 운영 노하우 |

Drop 가능성 메모는 리뷰 파일의 `## Drop 후보` 섹션에 1~3줄로 기록.

---

### 7. record.md status 업데이트

record.md가 `draft` 상태인 경우 Edit 도구로 frontmatter의 `status: draft`를 `status: live`로 수정한다.
이미 `live`면 변경 불필요.

---

### 8. ops 리뷰 파일 저장

**파일명**: `YYMMDD-ops-S{N}세션리뷰.md`
- `YYMMDD`: 세션 날짜 기준 (record.md `created` 필드)
- 예: `260322-ops-S00세션리뷰.md`

**저장 위치**: `01_ops/posts/`

**frontmatter**:

```yaml
---
title: "S{NN} 세션 리뷰 — YYYY-MM-DD"
kind: post
area: ops
status: draft
created: YYYY-MM-DD
target_channel: "#ops-general"
session_id: "S{NN}"
up: "[[MOC-운영]]"
tags:
  - ops/post
  - session/S{NN}
---
```

**파일 구조**:

```markdown
# S{NN} 세션 리뷰

## 목표 달성 여부

| 목표 | 결과 |
|------|------|
| ... | Y / N / 부분 |

## 회고 요약

### 잘된 것
- ...

### 아쉬운 것
- ...

### 멤버 반응
- ...

## 다음 세션 힌트

1. ...
2. ...
3. ...

## Drop 후보

- ...

---

## Discord 발행문 (`#ops-general`)

\```
🔧 **S{N} 세션 리뷰 — YYYY-MM-DD**

[발행문 전체]
\```

---

## 발행 체크리스트

- [ ] 내용 검토 완료
- [ ] `#ops-general`에 발행문 붙여넣기
- [ ] 발행 후 status → `live`
- [ ] Drop 후보 → `/drop-announce` 실행 (해당 항목 있을 경우)
```

---

### 9. 출력

- 저장된 파일 경로 안내
- Discord 발행문 전체를 대화창에 출력 (바로 복사 가능)
- 다음 세션 힌트 목록 간략 안내
- Drop 후보 있으면 `/drop-announce` 실행 여부 확인

---

## 워크플로우

```
/session-review S{NN}
   ↓
record.md + plan.md 읽기 → 목표 달성 점검 → 회고 정리
   ↓
ops 리뷰 초안 + 발행문 작성 → 01_ops/posts/ 저장
   ↓
사용자: 내용 확인 → #ops-general 발행문 붙여넣기
   ↓
발행 완료 후 status → live
   ↓
Drop 후보 있으면 → /drop-announce
```

---

## 관련 스킬

| 스킬 | 관계 |
|------|------|
| `session-ops record` | 선행 — 기록 완성 후 session-review 실행 |
| `discord-announce ops` | 후행 — ops 채널에 리뷰 게시 |
| `drop-announce` | 선택 — 세션 콘텐츠 → Drop 전환 |
| `memory-update` | 선택 — active-context, change-log 갱신 |

---

## 규칙

- 리뷰는 세션 종료 후 3일 이내 작성 권장 — 기억과 반응이 생생할 때
- 개인 식별 정보 금지 — 멤버 실명은 내부 파일(record.md)에만, 발행문에는 사용 안 함
- 로컬 문서 링크는 Discord에서 열리지 않으므로 요약 포함
- `01_ops/posts/` — 발행 전 초안. 발행 완료 후 status → `live` 처리
