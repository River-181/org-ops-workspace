---
name: org-init
description: 새 조직 볼트 초기 세팅 — 정체성 파일 생성 + 폴더 구조 적용
---

# /org-init — 조직 볼트 초기화

이 스킬은 `org-ops-workspace`를 처음 사용하는 조직을 위한 온보딩 스킬이다.
실행하면 조직 정체성 파일 6개를 생성하고, 세션 폴더 구조를 설정한다.

---

## Step 0: 재초기화 감지

`_system/identity/identity.md`를 읽는다.

`org_name:` 필드가 비어있지 않으면 이미 초기화된 볼트다:

```
이미 초기화된 볼트입니다 (조직명: [org_name]).
덮어쓰시겠습니까? 기존 정체성 파일이 모두 교체됩니다. (y/N)
```

- 사용자가 `y`를 입력하면: 덮어쓰기 모드로 계속
- 그 외 입력(N, 엔터, 기타): 중단

```
중단합니다. _system/identity/ 폴더의 파일을 직접 편집해 수정할 수 있습니다.
도움말: _system/identity/SETUP-GUIDE.md
```

---

## Step 1: 조직 유형 선택

사용자에게 다음을 표시하고 선택을 기다린다:

```
조직 유형을 선택하세요:

1. club            — 취미/관심사 클럽 (독서, 영화, 보드게임 등)
2. university-club — 대학 동아리 (학교 소속, 임원 구조)
3. startup         — 스타트업 팀 (제품/서비스, 성과 중심)
4. student-project — 대학생 팀플 (과제 마감 기반, 단기)
5. student-council — 학생회 (대표성, 임기제, 공식 회의)
6. sub-team        — 하위팀 (상위 조직 종속)
7. other           — 기타 (자유 형식)

번호 또는 이름을 입력하세요:
```

입력받은 값을 `org_type`으로 저장한다.

---

## Step 2: 정보 수집 방식 선택

```
정보 수집 방식을 선택하세요:

(A) 기존 문서가 있어요 — 파일 경로나 내용을 붙여주세요
(B) 질문에 답할게요 — 인터뷰 방식으로 진행

A 또는 B:
```

### Step 2A: 문서 제공 방식

사용자가 파일 경로를 제공하면 해당 파일을 읽는다.
여러 파일이면 모두 읽는다.
제공된 내용에서 아래 필드를 최대한 추출한다:
- org_name, org_topic, target_members, operating_cycle, size_range
- org_type별 추가 필드 (Step 2B 참고)
- 세션 유형, 주기, 결과물

추출 후 Step 3으로 이동.

### Step 2B: 인터뷰 방식

**공통 질문** (한번에 모두 묻기):

```
아래 질문에 답해주세요 (모르면 빈칸으로):

1. 조직 이름은 무엇인가요?
2. 어떤 주제/분야인가요? (예: PKM 스터디, 앱 개발, 독서 토론)
3. 주요 멤버 대상은 누구인가요? (예: 20대 직장인, ○○대 재학생)
4. 운영 주기는 어떻게 되나요? (예: 격주, 매주)
5. 현재 인원 규모는요? (예: 5-15명, 4명)
```

답변을 받은 후, **org_type별 추가 질문** (해당 유형만, 한번에):

- **club / university-club**:
  ```
  6. 모임을 뭐라고 부르나요? (세션, 정기모임, 스터디 등)
  7. [university-club만] 소속 학교/학과는 어디인가요?
  8. 모집은 어떻게 하나요? (상시 / 매 학기 초 / 초대제)
  ```

- **startup**:
  ```
  6. 팀 구성은 어떻게 되나요? (예: 개발 2, 디자인 1, PM 1)
  7. 현재 단계는? (아이디어 / MVP 개발 / 성장 / 스케일)
  8. 무엇을 만들고 있나요? (한 줄 설명)
  9. 스프린트 방식으로 운영하나요? 스프린트 길이는? (예: 2주)
  ```

- **student-project**:
  ```
  6. 과목명은 무엇인가요?
  7. 최종 제출/발표 마감일은 언제인가요? (YYYY-MM-DD)
  8. 최종 결과물 형태는? (논문 / 발표 / 앱 / 보고서 등)
  ```

- **student-council**:
  ```
  6. 소속 학교/학과는 어디인가요?
  7. 임기는 언제부터 언제까지인가요? (예: 2026-03 ~ 2027-02)
  8. 몇 대 학생회인가요? (예: 제15대)
  9. 주요 공약이나 슬로건이 있나요?
  ```

- **sub-team**:
  ```
  6. 상위 조직 이름은 무엇인가요?
  7. 이 팀의 담당 영역과 권한 범위는 무엇인가요?
  8. 보고 대상(역할명)은 누구인가요? (예: 대표, 운영진)
  ```

- **other**:
  ```
  6. 조직의 목적을 자유롭게 설명해주세요.
  7. 정기적인 모임이나 활동이 있나요? 있다면 어떻게 이루어지나요?
  ```

---

## Step 3: 정체성 파일 생성

### 생성할 파일 결정 (org_type별)

| org_type | 필수 생성 | 선택 확인 |
|----------|----------|----------|
| club | identity, soul, culture, workflow, platform, brand (6개) | — |
| university-club | identity, soul, culture, workflow, platform, brand (6개) | — |
| startup | identity, soul, culture, workflow, platform, brand (6개) | — |
| student-project | identity, soul, culture, workflow (4개) | platform, brand 생성? (y/N) |
| student-council | identity, soul, culture, workflow, platform (5개) | brand 생성? (y/N) |
| sub-team | identity, soul, culture, workflow (4개) | platform, brand 생성? (y/N) |
| other | identity, soul, culture, workflow (4개) | platform, brand 생성? (y/N) |

student-project / sub-team / other에게는 다음을 묻는다:
```
platform.md(협업 도구 설정)와 brand.md(브랜드 톤 설정)도 생성할까요?
조직 규모가 작거나 단기 프로젝트라면 필요 없을 수 있습니다. (y/N)
```

### 각 파일 작성

수집한 정보로 6개 파일을 작성한다. 파일 경로: `_system/identity/[파일명].md`

**`identity.md`** 예시 (필드가 없으면 `""` 그대로):
```yaml
---
title: "[org_name] — 조직 정체성"
status: live
created: [오늘 날짜 YYYY-MM-DD]
kind: system
area: system
tags:
  - system/identity
---

# 조직 기본 정보

org_name: "[수집한 값]"
org_type: "[수집한 값]"
org_topic: "[수집한 값]"
target_members: "[수집한 값]"
operating_cycle: "[수집한 값]"
size_range: "[수집한 값]"

# --- 유형별 선택 필드 ---
# (해당 없는 필드는 주석 처리)

[org_type별 조건부 필드 — 아래 참조]
```

org_type별 조건부 필드:
- university-club / student-council: `home_institution`, `recruitment_cycle`
- student-council: `mandate_period`, `council_generation`
- startup: `team_composition`, `current_stage`
- student-project: `course_name`, `deadline`
- sub-team: `parent_org`, `reporting_to`

**`workflow.md`** — 가장 중요:
```yaml
---
title: "[org_name] — 워크플로우"
status: live
created: [오늘 날짜]
kind: system
area: system
tags:
  - system/identity
---

session_type: "[세션/정기모임/회의/팀미팅/스프린트/팀회의 중 선택]"
frequency: "[수집한 값]"
duration: ""

output_artifacts:
  - plan.md
  - record.md

subfolder_pattern: "[org_type에 따른 기본값 — 아래 참조]"
```

`subfolder_pattern` 기본값 (수집한 정보 없을 경우):
- club / university-club → `S{NN}-{title}/`
- student-council / student-project / sub-team → `{YYMMDD}-{title}/`
- startup → `Sprint-{NN}/`
- other → `{YYMMDD}-{title}/`

**`soul.md`**, **`culture.md`**, **`platform.md`**, **`brand.md`**:
수집된 정보로 해당 필드를 채운다. 정보가 없는 필드는 `""` 유지.

### 검토 요청

모든 파일 작성 후:

```
✍️ 정체성 파일 초안 작성 완료

| 파일 | 주요 내용 |
|------|---------|
| identity.md | [org_name] / [org_type] / [size_range] |
| workflow.md | [session_type] / [frequency] / [subfolder_pattern] |
| soul.md | [why 한 줄] |
| culture.md | [tone] |
| platform.md | [platform_primary] |
| brand.md | [display_name] / [tagline] |

수정할 부분이 있으면 말씀해주세요. 없으면 다음 단계로 진행합니다.
```

수정 요청이 있으면 해당 파일을 수정 후 재표시.

---

## Step 4: 02_sessions/ 폴더 구조 설정

`_system/identity/workflow.md`의 `subfolder_pattern`과 `session_type`을 읽는다.

`02_sessions/README.md`를 업데이트한다:

```markdown
---
title: 세션 아카이브
status: live
created: [오늘 날짜]
---

# 세션 아카이브

이 폴더는 [org_name]의 모든 [session_type] 기록을 보관합니다.

## 폴더 명명 규칙

패턴: `[subfolder_pattern]`

예시:
- [패턴에 맞는 예시 1]
- [패턴에 맞는 예시 2]

## 새 세션 시작

`/session-setup`을 실행하세요.
```

---

## Step 5: CLAUDE.md 조직 정보 반영

`CLAUDE.md`의 `## Identity` 섹션 바로 아래에 다음 줄을 추가한다:

```markdown
> **[org_name]** — [org_type] | [org_topic]
> `/org-init` 완료됨. `_system/identity/`에서 수정 가능.
```

---

## Step 6: active-context.md 초기화

`_system/agents/memory/active-context.md`를 덮어쓴다:

```markdown
---
title: Active Context
status: live
created: [오늘 날짜]
---

# 현재 상태

**조직:** [org_name] ([org_type])
**주제:** [org_topic]
**초기화:** [오늘 날짜]

## 현재 Phase

볼트 초기화 완료. 첫 세션 준비 또는 운영 시작 가능.

## 다음 권장 작업

- `/session-setup` — 첫 [session_type] 폴더 생성
- `@ops` — 운영 조율 시작
- `_system/identity/` 파일을 편집해 세부 내용 보완 가능
```

---

## Step 7: 완료 메시지

```
✅ /org-init 완료

조직: [org_name]
유형: [org_type]
생성된 파일: _system/identity/ (N개)
세션 패턴: [subfolder_pattern]

다음 단계:
→ /platform-setup    — Discord 봇 + Notion API 연결 (AI가 단계별 안내)
→ /session-setup     — 첫 [session_type] 폴더 생성
→ @ops               — 주간 리뷰 또는 운영 시작
→ @prep              — 세션 기획 시작

Discord·Notion 자동화를 쓰려면 /platform-setup을 먼저 실행하세요.
_system/identity/ 파일을 직접 편집해 언제든 수정할 수 있습니다.
```

---

## 규칙

- 한 번에 너무 많은 질문을 하지 않는다 — 같은 맥락의 질문은 묶어서 물어본다
- 사용자가 답변을 모르면 빈칸("")으로 두고 진행한다
- 생성된 파일은 덮어쓰기 전 확인을 받는다
- 파일 작성 후 NUGI 특화 문자열(`내우주기`, `NUGI`, `누기`)이 포함되지 않았는지 확인한다
