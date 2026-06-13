---
name: session-close
description: 세션 종료 후 폴더 정리 + MOC·허브 파일·에이전트 메모리를 한 번에 갱신한다
argument-hint: "[세션ID]"
---

# session-close

세션 종료 후 마무리를 한 번에 완료한다. **닫기 = 세션을 done으로 확정하고 plan을 아카이브**.

1. 세션 폴더 파일 감사 (frontmatter, 네이밍, status) + plan.md archived
2. 허브 파일 갱신 (session_status → done, 링크 연결) · MOC-세션 갱신
3. 에이전트 메모리 갱신 (active-context, change-log)

**전제 조건**: `S{NN}-{슬러그}-record.md`가 `status: live` 상태일 것.

---

## 입력

- **세션 ID**: `NN` 또는 `S{NN}` (예: `01`, `S01`)

---

## 절차

### 1. 세션 폴더 파일 감사

`02_sessions/S{NN}-{슬러그}/` 전체 스캔 (하위 폴더 포함):

```bash
find "02_sessions/S{NN}-*" -name "*.md" | head -30
```

각 `.md` 파일에 대해 확인:

| 항목 | 기준 |
|------|------|
| frontmatter 3필드 | `title`, `status`, `created` 모두 존재 |
| status 값 | `draft` / `live` / `archived` 중 하나 |
| 네이밍 컨벤션 | `YYMMDD-*` 또는 세션 슬러그 패턴 |

**결과 분류**:
- `status: draft` 파일 → 목록 출력 (자동 변경 안 함, 사용자 확인 필요)
- frontmatter 누락 파일 → 경고 출력
- 네이밍 미준수 파일 → 경고 출력

record.md가 `status: live`인지 반드시 확인. draft면 사용자에게 알리고 중단.

**plan.md 아카이브** (닫기 전용 예외 처리):
`S{NN}-{슬러그}-plan.md`의 `status`를 `archived`로 변경한다. plan.md는 세션이 종료되면 그 목적이 소진되므로, 닫기 시점에 archived로 전환하는 것이 정상 흐름이다(일반 draft 파일 자동 변경 금지 원칙의 예외).

```yaml
status: archived   # plan.md 전용 — 닫기 완료 시 자동 적용
```

### 2. 허브 파일 갱신

`02_sessions/S{NN}-{슬러그}/S{NN}-{슬러그}.md` 읽기 후 갱신:

**session_status 전환** (닫기의 핵심 상태 확정):
`session_status`를 `done`으로 변경한다. `status`(문서 상태)는 `live`를 **유지**한다 — 허브 문서는 동결하지 않고 계속 참조 가능해야 한다.

```yaml
session_status: done   # prep → done (세션 종료 확정)
status: live           # 그대로 유지 — 허브 문서는 아카이브하지 않음
```

**확인·추가 항목**:
- `## 문서` 섹션: `[[S{NN}-{슬러그}-record]]` 링크 존재 여부
- `## 발표 / 자료` 섹션: 정제 스크립트, 세션 정리 파일 링크 추가

```markdown
| 세션 스크립트 (정제) | `[[YYMMDD-S{NN}-세션스크립트]]` | Markdown |
| 세션 정리 (요약) | `[[YYMMDD-S{NN}-세션정리]]` | Markdown |
```

- 허브 파일 `status:`가 `live`인지 확인. 아니면 `live`로 갱신.

### 3. MOC-세션 갱신

`_system/obsidian/mocs/MOC-세션.md` 파일 확인:

**파일이 없으면 생성**:
```yaml
---
title: MOC-세션
kind: moc
area: sessions
status: live
created: {오늘 날짜}
up: "[[MOC-홈]]"
tags:
  - type/moc
---

# MOC-세션

[클럽명] 세션 전체 인덱스.

## 세션 목록

| 세션 | 제목 | 날짜 | 허브 |
|------|------|------|------|
```

**세션 항목 추가**:
```
| S{NN} | {제목} | {날짜} | [[S{NN}-{슬러그}]] |
```

날짜는 세션 실시일 기준 `YYYY-MM-DD` 형식.

### 4. 칸반 보드 동기화

세션 hub 파일의 `session_status`가 갱신되었으므로 칸반 보드를 동기화한다:

```bash
_system/tools/kanban/kanban-sync.sh
```

**목적**: 방향 1(칸반→frontmatter)과 방향 2(파일→칸반) 모두 실행.
허브 파일의 session_status가 변경된 경우 칸반 보드도 즉시 일치하게 유지된다.

### 5. 에이전트 메모리 갱신

**active-context.md** 갱신 (덮어쓰기):
- `현재 진행 세션` → S{NN} 완료 처리
- 다음 세션 힌트 반영 (record.md의 액션 아이템 참조)

**change-log.md** append:
```markdown
### {YYYY-MM-DD}

- S{NN} 세션 완료 — "{제목}", 참석 {N}명
  - 정제 스크립트·세션 정리 완료
  - member-share-space 패키징 완료
  - record.md live
  - 다음: {record.md의 주요 액션 아이템 1-2개}
```

### 6. 클럽 타임라인 갱신 (선택)

기념할 만한 마일스톤이면 `01_ops/strategy/260314-클럽타임라인.md`에 추가:

```markdown
- {YYYY-MM-DD}: S{NN} "{제목}" 진행 — 참석 {N}명, 실습 완료율 {X}%
```

세션이 클럽 역사에 기록할 만한 첫 번째 사례이거나 중요한 전환점인 경우 추가.

---

## 완료 보고

```
✅ session-close 완료 — S{NN}

📋 파일 감사
  정상: {N}개 파일
  ✓ plan.md → archived (닫기 완료)
  ⚠️ draft 상태: {파일 목록} (사용자 확인 필요)
  ⚠️ frontmatter 누락: {파일 목록}

🔗 갱신된 파일
  ✓ 허브 파일: 02_sessions/S{NN}-{슬러그}/S{NN}-{슬러그}.md
      session_status: done / status: live 유지
  ✓ MOC-세션: _system/obsidian/mocs/MOC-세션.md
  ✓ 칸반 보드 동기화 완료
  ✓ active-context.md
  ✓ change-log.md

💡 남은 draft 파일 ({N}개):
  {파일1}
  {파일2}
```

---

## 주의사항

- record.md가 live가 아니면 실행을 중단한다 (`/session-ops record`로 먼저 완성)
- draft 파일을 자동으로 live로 변경하지 않는다 — 사용자가 검토 후 결정
- active-context.md는 덮어쓰기 — 기존 내용을 읽고 업데이트하여 최신 상태로 유지

---

## 연관 스킬

| 스킬 | 관계 |
|------|------|
| `/session-ops record {세션폴더}` | 전제 — record.md 완성 |
| `/session-package {세션ID}` | 병행 — 공유 폴더 패키징 |
| `/session-transcript {세션ID}` | 병행 — 정제본·요약본 생성 |
| `/session-review {세션ID}` | 이후 선택 — 운영팀 회고 |
| `/memory-update` | 이후 선택 — 심화 메모리 갱신 |

---

## 세션 종료 후 권장 순서

```
/session-ops record {세션폴더}     → record.md 완성
/session-package {세션ID}          → 공유 폴더 패키징
/session-transcript {세션ID}       → 녹음본 정제·요약
/session-close {세션ID}            → 폴더 정리 + 링크 갱신  ← 이 스킬
/session-review {세션ID}           → 운영팀 회고 (선택)
```
