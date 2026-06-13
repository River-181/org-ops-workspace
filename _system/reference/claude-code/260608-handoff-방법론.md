---
title: "handoff 방법론 (Matt Pocock)"
kind: reference
area: system
status: live
created: 2026-06-08
updated: 2026-06-08
tags:
  - system/reference
  - platform/claude-code
  - type/workflow
up: "[[_system/reference/README]]"
source: "Matt Pocock, '/handoff is my new favourite skill' (YouTube, 2026-05, ~12분) https://youtu.be/dtAJ2dOd3ko"
---

# handoff 방법론 (Matt Pocock)

> 2026-06-08 조사 기준. Matt Pocock의 YouTube 영상 핵심 정리.

---

## 핵심 개념

### handoff란?

`/handoff` — 현재 세션의 컨텍스트를 마크다운 문서로 압축해 OS 임시 디렉토리(`$TMPDIR`)에 저장 → 새 세션 에이전트가 이어받는 스킬. **일회용(disposable)**.

### compact와의 차이

컨텍스트 윈도우는 두 영역으로 나뉨:
- **smart zone** (초반 ~120k 토큰) — 모델이 정교하게 처리
- **dumb zone** (이후) — 성능 저하

| | compact | handoff |
|---|---------|---------|
| **용도** | 한 세션을 요약해 이어가며 누적 | 맥락을 다른 세션으로 넘겨 현재 세션 순수 유지 |
| **저장** | 현재 세션 내 | $TMPDIR (휘발성) |
| **대상** | 긴 단일 세션, 디버깅 반복 | 병렬 작업, 부모→자식 세션 |
| **메모리** | 퇴적층 쌓임 | 세션별 정리, 반송 시에만 합침 |

### DIY 서브에이전트 패턴

부모 세션 → `handoff` (MD 문서) → 자식 세션(프로토타입/버그픽스, 더 큰 컨텍스트 가능) → 자식이 학습을 `handoff` 문서로 부모에 반송.

컨텍스트를 한 작업에 쓰고 압축해 부모로 돌려보냄. 부모는 계속 깨끗한 상태 유지.

### 하니스 무관

마크다운 형식이라 Claude Code → Codex/Copilot 교차 가능. Adversarial review(다른 모델) 등도 호환.

---

## 스킬 설계 6원칙

### 1. 현재 대화 요약

새 에이전트가 이어받을 수 있도록 핵심을 압축:
- 무엇을 하고 있었는가?
- 어디까지 진행했는가?
- 남은 작업은 무엇인가?

### 2. `$TMPDIR`에 저장

실제 저장 위치: OS 임시 디렉토리
- 목적: 코드베이스에 rot(썩은 데이터) 금지
- 특징: 일회용, 세션 종료 후 자동 정리

### 3. "추천 스킬" 섹션 포함

다음 세션이 부를 스킬을 명시:
```
## 다음 단계

이어받을 스킬:
- `/prototype` — 프로토타입 구현
- `/codex:gpt-5-4-prompting` — 프롬프트 튜닝
```
→ 새 세션 에이전트가 자동으로 해당 스킬의 flavor을 로드

### 4. 기존 아티팩트 중복 금지

포인터(링크/이슈 번호)로 가리키고 복사하지 않음:
```markdown
참고 파일: 
- `src/components/Button.tsx` (기존 코드)
- GitHub issue #123 (관련 요구사항)
```

### 5. 민감정보 redact

API 키, 비밀번호, PII(개인식별정보) 제거:
```markdown
❌ API_KEY=sk-proj-xxxxx
✓ API_KEY=<REDACTED>
```

### 6. 인자로 목적 지정

`/handoff 목적` — 핸드오프할 때 **항상 목적을 명시**:
```
/handoff "프로토타입 세션에 부하 테스트 작업 위임"
```
→ 마크다운 문서가 그 목적에 맞춰 생성되고, 자식 세션이 명확한 지시를 받음

---

## 활용 패턴

### 패턴 1: 기획 중 범위 밖 작업 분리

기획 세션 중 버그/리팩토링이 발견되면:
1. 해당 작업을 handoff로 분리 세션 생성
2. 현재 기획 세션은 오히려 더 선명해짐
3. 자식 세션이 문제 해결 후 반송 → 기획 계속

장점: 병렬 진행, 컨텍스트 오염 방지

### 패턴 2: 어려운 부분 프로토타입

복잡한 기술 선택/구현이 필요할 때:
1. 현재 세션: 전체 설계만 진행
2. handoff → 프로토타입 세션: 그 부분만 깊게 탐구
3. 프로토타입 세션: 학습 정리해 handoff로 반송
4. 현재 세션: 반송 자료 읽고 계속

---

## 적용 안내

### 메모리 관리와 handoff 구분

| | handoff | 메모리 시스템 |
|---|---------|---------|
| **용도** | 세션 간 바톤 패스 | 클럽 영속 기억 |
| **저장** | $TMPDIR (휘발성) | `_system/agents/memory/` (영속) |
| **범위** | 현재 작업만 | 클럽 전체 맥락 |
| **회전** | 일회용 | active-context 갱신 + change-log append |

### 구현 로드맵

`.claude/skills/handoff/` 스킬로 구현:
- 자동 프롬프트: 현재 세션 요약 → MD 생성 → $TMPDIR 저장
- 자동 프롬프트: "다음 단계" 섹션에서 추천 스킬 추출
- 수동 호출: `/handoff "목적 명시"` 

### 세션 운영 규칙

- 세션팀: 병렬 작업(리서치 + 슬라이드)은 handoff로 분리 → 각각 병렬 처리
- 에이전트 위임: 에이전트 위임 시 현재 상태를 handoff로 압축 → 자식 에이전트의 컨텍스트 확보
- 프로토타입: 신기능 설계 중 미검증 부분은 별도 프로토타입 handoff → 학습 반송

---

## 참고

- YouTube: https://youtu.be/dtAJ2dOd3ko
- 시청 권장: Claude Code 스킬 개발자 및 에이전트 오케스트레이션 설계자
