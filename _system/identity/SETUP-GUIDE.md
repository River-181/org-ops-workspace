---
title: Obsidian 전용 셋업 가이드
status: live
created: 2026-03-23
kind: system
area: system
tags:
  - system/identity
---

# Obsidian 전용 셋업 가이드

> Claude Code 없이 **Obsidian만으로** 이 워크스페이스를 설정하는 가이드입니다.
> Claude Code를 사용 중이라면 `/org-init`을 실행하세요 — 아래 과정을 자동으로 처리합니다.

---

## 시작 전에

이 폴더(`_system/identity/`)에는 조직의 **정체성 파일** 6개가 있습니다.
이 파일들은 에이전트(AI 어시스턴트)가 조직을 이해하는 데 사용하는 "조직 메모리"입니다.

파일을 채우면:
- AI 에이전트가 조직 이름, 유형, 문화를 알고 일관된 방식으로 도움을 줍니다
- 세션 폴더 구조가 조직에 맞게 자동 설정됩니다
- 문서 작성 톤이 조직 분위기에 맞춰집니다

---

## 설정 순서

아래 순서대로 파일을 작성하세요. **필수 파일** 4개만 채워도 기본 작동합니다.

```
1순위 (필수) → identity.md   — 조직 기본 정보
2순위 (필수) → workflow.md   — 세션/회의 구조
3순위 (권장) → soul.md       — 존재 이유와 미션
4순위 (권장) → culture.md    — 소통 문화와 톤앤매너
5순위 (선택) → platform.md   — 협업 도구 설정
6순위 (선택) → brand.md      — 브랜드 톤과 비주얼 방향
```

팀플·하위팀이라면 5, 6번은 건너뛰어도 됩니다.

---

## 1단계: identity.md 작성

가장 중요한 파일입니다. 모든 에이전트가 부팅 시 이 파일을 읽습니다.

**필수 필드:**

| 필드 | 설명 | 예시 |
|------|------|------|
| `org_name` | 조직 이름 | "[클럽명] 스터디" |
| `org_type` | 조직 유형 | `club` |
| `org_topic` | 주제/분야 | "PKM과 디지털 생산성" |
| `target_members` | 멤버 대상 | "20대 직장인, 대학원생" |
| `operating_cycle` | 운영 주기 | "격주 토요일" |
| `size_range` | 인원 규모 | "8-12명" |

**org_type 선택지:**
- `club` — 취미/관심사 클럽
- `university-club` — 대학 동아리
- `startup` — 스타트업 팀
- `student-project` — 대학생 팀플
- `student-council` — 학생회
- `sub-team` — 하위팀
- `other` — 기타

**org_type별 추가 필드:**

*university-club / student-council:*
- `home_institution`: 소속 학교/학과 (예: "○○대학교 심리학과")

*student-council:*
- `mandate_period`: 임기 (예: "2026-03 ~ 2027-02")
- `council_generation`: 기수 (예: "제15대")

*startup:*
- `team_composition`: 팀 구성 (예: "개발 2, 디자인 1, PM 1")
- `current_stage`: 단계 (`ideation` / `mvp` / `growth` / `scale`)

*student-project:*
- `course_name`: 과목명
- `deadline`: 마감일 (YYYY-MM-DD)

*sub-team:*
- `parent_org`: 상위 조직명
- `reporting_to`: 보고 대상 역할

---

## 2단계: workflow.md 작성

세션/회의 구조를 정의합니다. **`subfolder_pattern`이 02_sessions/ 폴더 구조를 결정합니다.**

**필수 필드:**

| 필드 | 설명 | 예시 |
|------|------|------|
| `session_type` | 모임 유형 이름 | "세션", "정기모임", "회의", "스프린트" |
| `frequency` | 운영 주기 | "격주 토요일 오후 2시" |
| `output_artifacts` | 각 세션에서 생성할 파일 | `plan.md`, `record.md` |
| `subfolder_pattern` | 세션 폴더 이름 패턴 | `S{NN}-{title}/` |

**subfolder_pattern 선택 가이드:**

| 패턴 | 예시 폴더명 | 권장 조직 유형 |
|------|-----------|--------------|
| `S{NN}-{title}/` | `S01-시작세션/` | 클럽, 동아리 (회차 중심) |
| `{YYMMDD}-{title}/` | `260315-1차회의/` | 학생회, 팀플, 하위팀 (날짜 중심) |
| `Sprint-{NN}/` | `Sprint-01/` | 스타트업 |
| `M{NN}-{YYMMDD}/` | `M01-260315/` | 동아리 (모임 번호 + 날짜) |

---

## 3단계: soul.md 작성 (권장)

조직의 존재 이유와 미션을 담습니다.

**주요 필드:**
- `why`: 왜 이 조직이 존재하는가 (1-3문장)
- `mission`: 미션 한 줄
- `values`: 핵심 가치 목록 (예: `[자유로운 탐구, 실패 환영, 공유]`)
- `differentiator`: 비슷한 조직과의 차이점

팀플·하위팀이라면 `mission`과 `deliverable_type`(결과물 유형)이 핵심입니다.

---

## 4단계: culture.md 작성 (권장)

소통 문화와 AI가 문서를 작성할 때 사용할 톤을 정의합니다.

**주요 필드:**
- `tone`: 소통 톤 (예: "친근함", "전문적", "유머 있는")
- `language`: `ko` (한국어) / `en` / `ko-en-mixed`
- `communication_style`: 소통 방식 (예: "직접 피드백", "토론 중심")
- `taboos`: 금기 사항 목록

---

## 5단계: 02_sessions/ 폴더 만들기

`workflow.md`에서 `subfolder_pattern`을 결정했으면, 실제 폴더를 직접 만드세요.

**패턴이 `S{NN}-{title}/`인 경우:**
```
02_sessions/
├── S01-킥오프/
│   ├── S01-킥오프-plan.md      ← 세션 기획 (tpl-세션기획.md 참고)
│   └── S01-킥오프-record.md   ← 세션 기록 (tpl-세션기록.md 참고)
├── backlog/
│   └── README.md
└── README.md
```

**패턴이 `{YYMMDD}-{title}/`인 경우:**
```
02_sessions/
├── 260315-1차회의/
│   ├── 260315-1차회의-plan.md
│   └── 260315-1차회의-record.md
└── backlog/
```

각 세션 폴더 내 파일은 `01_ops/templates/`의 템플릿을 참고하세요:
- 세션 기획: `01_ops/templates/tpl-세션기획.md`
- 세션 기록: `01_ops/templates/tpl-세션기록.md`

---

## 6단계: platform.md / brand.md (선택)

**platform.md:** 협업 도구를 정의합니다.
- `platform_primary`: 주요 소통 도구 (KakaoTalk, Discord, Slack 등)
- `platform_collaboration`: 협업 도구 (Notion, Google Drive 등)
- 팀플이나 하위팀이라면 건너뛰어도 됩니다.

**brand.md:** 브랜드 정체성을 정의합니다.
- `display_name`, `short_name`, `tagline`: 조직 표시 이름과 슬로건
- `visual_style`, `content_voice`: AI가 콘텐츠 생성 시 참조
- 내부 운영만 하는 조직이라면 건너뛰어도 됩니다.

---

## 설정 완료 체크리스트

- [ ] `identity.md` — org_name, org_type, org_topic, target_members, operating_cycle, size_range 모두 채움
- [ ] `workflow.md` — session_type, frequency, output_artifacts, subfolder_pattern 채움
- [ ] `soul.md` — why, mission 최소 작성
- [ ] `culture.md` — tone, language 최소 작성
- [ ] `02_sessions/` 폴더 구조 생성 (workflow.md 패턴에 맞게)
- [ ] `_system/agents/memory/active-context.md` — 조직명과 날짜로 초기 내용 작성

---

## 도움이 필요하다면

Claude Code를 사용할 수 있다면 언제든지 `/org-init`을 실행해 재설정할 수 있습니다.
이미 파일을 작성했어도 덮어쓰기 전에 확인을 요청합니다.

각 파일의 자세한 필드 설명은 해당 파일 상단 주석을 참고하세요.
