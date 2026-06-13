---
name: project-close
description: 프로젝트(PRJ-*) 종료 후 PROGRESS 최종화 + 허브 frontmatter 정합 + CLAUDE.md 참조 갱신 + 에이전트 메모리 갱신을 한 번에 완료한다
argument-hint: "[프로젝트ID]"
created: 2026-06-11
status: live
---

# project-close

프로젝트를 닫을 때의 6단계 마무리를 한 번에 완료한다. **닫기 = 프로젝트를 done/archived로 확정하고 볼트 전체 참조를 정합화**.

1. 프로젝트 폴더 감사 — 완료 조건 충족 여부 확인 (미완이면 중단)
2. PROGRESS.md 최종화 — 완료 마일스톤 확정 + 종료 로그
3. 허브 frontmatter 정합 — `project_status` + `status` 동시 갱신
4. MOC-프로젝트 반영 — frontmatter 갱신으로 자동 처리 (수동 편집 불필요)
5. CLAUDE.md 점검 — Active Project 참조 갱신/제거
6. 에이전트 메모리 갱신 — active-context, change-log

**전제 조건**: 프로젝트 폴더가 `03_projects/PRJ-{이름}/` 구조이고, 허브 파일(`PRJ-{이름}.md`)이 존재할 것.

---

## 입력

- **프로젝트 ID**: `PRJ-이름` 형식 (예: `PRJ-볼트v3`, `PRJ-메모리OS`)
- **종료 유형** (선택): `done`(완수) 또는 `archived`(중단·대체). 기본값 `done`.

---

## 절차

### 1. 프로젝트 폴더 감사

`03_projects/PRJ-{이름}/` 전체 스캔:

```bash
find "03_projects/PRJ-{이름}" -name "*.md" | head -30
```

**완료 조건 확인** — 아래 중 하나라도 미충족이면 닫기를 **중단**하고 사용자에게 알린다:

| 항목 | 기준 |
|------|------|
| PROGRESS.md 전체 완료 | 모든 작업 항목이 `done` 상태 (미완 `todo`/`in-progress` 없음) |
| PLAN.md 미결 사항 | `## 미결 사항` 섹션이 비어 있거나 존재하지 않음 |
| 허브 파일 존재 | `PRJ-{이름}.md` 파일 확인 |

미완 작업이 남아 있다면:

```
⛔ 닫기 중단 — 미완 작업 {N}개 발견
  - {작업 목록}
미완 작업을 먼저 완료하거나, 의도적 중단이면 종료 유형 'archived'를 명시하세요.
```

단, 종료 유형이 `archived`(중단·대체)인 경우는 미완 작업이 있어도 진행한다.

### 2. PROGRESS.md 최종화

`03_projects/PRJ-{이름}/PROGRESS.md` 갱신:

**확인·추가 항목**:
- `## 완료 마일스톤` 섹션: 주요 달성 사항 목록이 완성되어 있는지 확인. 누락된 항목이 있으면 추가.
- `## 작업 로그` 마지막 항목에 종료 기록 append:

```markdown
### {YYYY-MM-DD} — 프로젝트 종료

- 종료 유형: {done | archived}
- 종료 사유: {간략한 사유 — 완수 / 대체 프로젝트 출범 / 방향 전환 등}
- 최종 상태: 전체 Phase {N}개 완료 / 미완 {N}개 (archived 시)
```

### 3. 허브 frontmatter 정합

`03_projects/PRJ-{이름}/PRJ-{이름}.md`의 frontmatter 갱신:

**두 필드의 의미 차이**:
- `project_status`: 프로젝트 **생애주기** 상태 — `active` / `done` / `paused` / `archived`. MOC-프로젝트와 `projects.base` 상태별 뷰가 이 필드로 분류한다.
- `status`: **문서** 상태 — `draft` / `live` / `archived`. 닫기 시 `archived`로 변경해 문서를 동결한다.

```yaml
project_status: done      # 완수의 경우 — 또는 archived(중단·대체)
status: archived          # 문서 동결 — 닫힌 프로젝트 허브는 더 이상 편집하지 않음
updated: {오늘 날짜}
```

`status: archived`는 세션 허브와 다른 점이다. 세션 허브(`session_status: done`)는 `status: live`를 유지하지만, 프로젝트 허브는 종료 후 문서를 동결하는 것이 원칙이다.

### 4. MOC-프로젝트 반영

`_system/obsidian/mocs/MOC-프로젝트.md`를 **수동으로 편집하지 않아도 된다**.

`projects.base`는 `project_status` 필드로 `groupBy`하는 `상태별` 뷰(`![[projects.base#상태별]]`)를 갖고 있다. Step 3에서 frontmatter를 정확히 갱신하면 Obsidian이 자동으로 해당 프로젝트를 `done` 또는 `archived` 그룹에 배치한다.

**확인만 권장**: MOC-프로젝트.md에 해당 프로젝트가 섹션별로 수동 목록화된 경우, 섹션 이동이 필요할 수 있다. 확인 후 필요하면 갱신.

### 5. CLAUDE.md 점검 ← 핵심 정합 단계

닫는 프로젝트가 `CLAUDE.md`에서 활성 참조되고 있으면 반드시 갱신한다. 참조를 남겨두면 에이전트가 종료된 프로젝트를 계속 활성으로 인식한다.

**체크 항목**:

- [ ] `## Active Project: PRJ-{이름}` 섹션 — 존재하면 제거하거나 `## 완료 프로젝트` 등으로 전환
- [ ] `### 에이전트 시작 프로토콜` — 해당 프로젝트의 PLAN.md·PROGRESS.md를 먼저 읽으라는 지시가 있으면 제거
- [ ] `### Phase 로드맵` — 활성 프로젝트로 기술된 Phase 현황이 있으면 "완료" 상태로 업데이트하거나 섹션 제거
- [ ] 스킬 표·에이전트 표·레퍼런스 표 — 해당 프로젝트를 직접 지시하는 항목이 있으면 갱신

> **예시 (PRJ-볼트v3)**: `CLAUDE.md`의 `## Active Project: PRJ-볼트v3` 전체 섹션과 에이전트 시작 프로토콜의 두 파일 읽기 지시를 제거한다.

### 6. 에이전트 메모리 갱신

**active-context.md** 갱신 (덮어쓰기):
- `현재 활성 프로젝트` 목록에서 해당 프로젝트 제거
- 관련 진행 중 작업 항목 정리

**change-log.md** append:
```markdown
### {YYYY-MM-DD}

- PRJ-{이름} 프로젝트 종료 ({done | archived}) — "{프로젝트 제목}"
  - 종료 사유: {간략 사유}
  - PROGRESS.md 최종화 완료
  - 허브 project_status: {done|archived}, status: archived
  - CLAUDE.md Active Project 참조 제거
```

**클럽 타임라인 갱신 (선택)**:
프로젝트가 클럽 역사에 기록할 만한 마일스톤이면 `01_ops/strategy/260314-클럽타임라인.md`에 추가:

```markdown
- {YYYY-MM-DD}: PRJ-{이름} "{제목}" 완료 — {1줄 성과 요약}
```

---

## 완료 보고

```
✅ project-close 완료 — PRJ-{이름}

📋 폴더 감사
  완료 작업: {N}개 / 전체 {N}개
  ✓ PLAN.md 미결 사항: 없음

📄 갱신된 파일
  ✓ PROGRESS.md: 종료 로그 추가
  ✓ 허브 frontmatter: project_status={done|archived}, status=archived
  ✓ CLAUDE.md: Active Project 섹션 제거/갱신
  ✓ active-context.md
  ✓ change-log.md

💡 선택 후속 작업:
  - 클럽 타임라인 기록 (마일스톤인 경우)
  - 관련 에이전트 메모리 심화 갱신 (/memory-update)
```

---

## 주의사항

- 미완 작업이 있고 종료 유형이 `done`이면 실행을 중단한다. `archived`로 명시하거나 작업을 먼저 완료할 것.
- `status: archived`는 문서 동결을 의미한다 — 이후 해당 허브 파일을 편집하지 않는 것을 원칙으로 한다.
- CLAUDE.md 참조 제거는 **반드시** 수행한다. 누락 시 에이전트가 종료 프로젝트를 계속 활성으로 읽는다.
- active-context.md는 덮어쓰기 — 기존 내용을 읽고 업데이트하여 최신 상태로 유지.

---

## 연관 스킬

| 스킬 | 관계 |
|------|------|
| `/progress-update` | 전제 권장 — 닫기 전 PROGRESS.md 최신화 |
| `/vault-health` | 병행 선택 — 닫기 후 전체 볼트 정합성 점검 |
| `/memory-update` | 이후 선택 — 심화 메모리 갱신 |
| `/session-close {세션ID}` | 유사 구조 — 세션 닫기 참조 |

---

## 프로젝트 종료 권장 순서

```
/progress-update {프로젝트ID}    → PROGRESS.md 최신화 확인
/project-close {프로젝트ID}      → 폴더 감사 + 허브 정합 + CLAUDE.md 갱신  ← 이 스킬
/memory-update                   → 심화 메모리 갱신 (선택)
```
