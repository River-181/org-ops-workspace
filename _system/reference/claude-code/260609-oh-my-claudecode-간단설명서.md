---
title: oh-my-claudecode 간단설명서
kind: reference
area: system
status: draft
created: 2026-06-09
up: "[[_system/reference/README]]"
tags: [platform/claude-code, type/guide]
---

# oh-my-claudecode (OMC) 간단설명서

## 개요

**oh-my-claudecode**(별칭: **omc**)는 Claude Code를 위한 멀티 에이전트 오케스트레이션 플러그인이다. "Claude Code를 배우지 마세요. 그냥 OMC를 쓰세요"라는 철학 아래, 자동화된 계획·실행·검증·개선 워크플로우를 제공한다. 자연어 명령 하나로 복잡한 개발 작업을 처리하며, 터미널 CLI(`omc` 커맨드)와 세션 내 스킬(`/...`)을 동시에 지원한다.

**설치 후 `/reload-plugins` 실행이 필요하다.**

- **출처**: [Yeachan-Heo/oh-my-claudecode](https://github.com/Yeachan-Heo/oh-my-claudecode)
- **공식 문서**: https://yeachan-heo.github.io/oh-my-claudecode-website
- **커뮤니티**: [Discord](https://discord.gg/sj4exxQ9v)

---

## 핵심 커맨드 — 용도별 그룹

### 자율 실행 (자동으로 끝까지)

| 커맨드 | 설명 |
|--------|------|
| `/autopilot` | 아이디어에서 작동하는 코드까지 완전 자율 실행 |
| `/ralph` | 완료까지 자체 검증·개선 루프 (리뷰어 커스터마이징 가능) |
| `/ultrawork` | 고처량 병렬 태스크 집행 엔진 |
| `/team` | N개 에이전트가 공유 태스크 리스트로 협력 (Claude Code 네이티브 팀) |

### 계획·리서치 (실행 전 정보 수집)

| 커맨드 | 설명 |
|--------|------|
| `/plan` | 전략적 계획 (선택: 인터뷰 워크플로우 포함) |
| `/ralplan` | 합의 기반 계획 (모호한 요청을 실행 전 자동 검증) |
| `/deep-interview` | 소크라테스식 심층 인터뷰 — 코드 작성 전 요구사항 명확화 |
| `/deep-dive` | 2단계 파이프라인: 원인 추적(trace) → 요구사항 명확화(deep-interview) |
| `/trace` | 증거 기반 추적 — 경쟁 가설을 동시 실행하여 최적 원인 도출 |
| `/sciomc` | 병렬 과학자 에이전트 조직 — 종합 분석 |
| `/external-context` | 병렬 문서 전문가 — 웹 검색 + 문서 조회 |

### 품질 보증·검증 (테스트·비교·정리)

| 커맨드 | 설명 |
|--------|------|
| `/ultraqa` | QA 사이클링 워크플로우 — 테스트→검증→수정 반복 |
| `/verify` | 변경 사항이 정말 작동하는지 확인 (완료 전) |
| `/visual-verdict` | 스크린샷 비교 기반 시각적 QA 판정 |
| `/ai-slop-cleaner` | AI 생성 코드 정리 — 회귀 안전 + 삭제 우선 워크플로우 |

### 코딩·개발 환경 (저장소·릴리즈·스킬)

| 커맨드 | 설명 |
|--------|------|
| `/deepinit` | 깊은 저장소 초기화 — 계층적 AGENTS.md 문서 생성 |
| `/project-session-manager` (또는 `/psm`) | Worktree 기반 개발 환경 — 이슈/PR/기능별 터미널 세션 |
| `/release` | 릴리즈 어시스턴트 — 규칙 분석→캐시→가이드 자동화 |
| `/skillify` | 현 세션의 반복 워크플로우를 재사용 가능한 스킬로 변환 |

### 고급 자율 개선 (반복·최적화)

| 커맨드 | 설명 |
|--------|------|
| `/autoresearch` | 상태 유지 단일 미션 개선 루프 — 평가자 계약·마크다운 결정 로그·최대 런타임 정지 |
| `/self-improve` | 자율 진화 코드 개선 — 토너먼트 선택 방식 |
| `/ultragoal` | 지속 다중 목표 워크플로우 — `.omc/ultragoal` 아티팩트 + `/goal` 핸드오프 |

### 협력·모델 선택 (마진 평가·다중 모델)

| 커맨드 | 설명 |
|--------|------|
| `/ask` | 프로세스 우선 어드바이저 라우팅 — Claude/Codex/Gemini 선택 가능 |
| `/ccg` | Claude-Codex-Gemini 삼중 오케스트레이션 (Codex + Gemini 각각 병렬→Claude 통합) |

### 지식·메모리 (누적 학습)

| 커맨드 | 설명 |
|--------|------|
| `/wiki` | LLM Wiki — 세션을 넘어 누적되는 마크다운 지식베이스 |
| `/remember` | 프로젝트 지식 검토 — 메모리/노트패드/문서 중 적절한 곳에 기록 |
| `/writer-memory` | 작가 전용 메모리 — 인물·관계·장면·테마 추적 |
| `/learner` | 현 대화에서 배운 내용을 재사용 가능한 스킬로 추출 |

### 진단·유지보수 (설치·복구)

| 커맨드 | 설명 |
|--------|------|
| `/setup` | 설치/업데이트 라우팅 — setup/doctor/MCP 중 적절한 흐름으로 분기 |
| `/omc-setup` | oh-my-claudecode 설치 또는 갱신 (플러그인/npm/로컬 개발) |
| `/omc-doctor` | OMC 설치 문제 진단·자동 수정 |
| `/debug` | 현 세션/저장소 상태 진단 — 로그·추적·재현 가능 |
| `/cancel` | 활성 OMC 모드 취소 (autopilot/ralph/ultrawork 등) |

### 설정·통합 (외부 연결)

| 커맨드 | 설명 |
|--------|------|
| `/configure-notifications` | 알림 통합 설정 — Telegram/Discord/Slack (자연어로) |
| `/mcp-setup` | 인기 MCP 서버 설정 |
| `/omc-teams` | CLI 팀 런타임 — tmux 분할 창에서 claude/codex/gemini 워커 |
| `/hud` | HUD 디스플레이 옵션 설정 (레이아웃·프리셋·요소) |

### 참고·유틸 (레퍼런스·관리)

| 커맨드 | 설명 |
|--------|------|
| `/omc-reference` | OMC 에이전트 카탈로그·사용 가능 도구·팀 파이프라인·커밋 프로토콜·스킬 레지스트리 |
| `/skill` | 로컬 스킬 관리 — 목록/추가/삭제/검색/편집 |
| `/local-build-reminder` | OMC 로컬 포크 수정 시 재빌드 알림 |

---

## 빠른 시작 — 처음 써볼 커맨드 3가지

### 1. 설정 (첫 실행)
```
/omc-setup
```
OMC를 처음 사용할 때 한 번만 실행. 필요한 디렉토리와 설정을 자동 생성한다.

### 2. 상태 확인 (문제 있을 때)
```
/omc-doctor
```
설치 문제를 자동 진단하고 수정을 제안한다.

### 3. 첫 작업 시작 (무엇부터?)
```
/deep-interview "내가 만들고 싶은 것" 또는 /ralplan
```
요구사항이 불명확하면 먼저 deep-interview로 생각을 정리한 후, 실행을 시작한다.

---

## 주요 특징

- **학습 곡선 제로**: 자연어 명령 하나로 복잡한 오케스트레이션이 자동 진행
- **멀티 서피스**: 터미널 CLI(`omc`) + 세션 내 스킬(`/...`) 모두 지원
- **팀 기반 병렬 실행**: Claude Code 네이티브 팀으로 여러 에이전트 동시 작동
- **지속적 개선**: 자율 검증·반복·최적화로 완성도 높은 결과물 생성
- **마크다운 결정 로그**: 모든 의사결정이 `.omc/` 폴더에 추적 가능하도록 저장

---

## 설치 방법

### 플러그인 설치 (권장)
```bash
/plugin marketplace add https://github.com/Yeachan-Heo/oh-my-claudecode
/plugin install oh-my-claudecode
/reload-plugins
/omc-setup
```

### npm 전역 설치
```bash
npm i -g oh-my-claude-sisyphus@latest
omc setup
```

---

## 더 알아보기

- **공식 문서**: https://yeachan-heo.github.io/oh-my-claudecode-website
- **CLI 레퍼런스**: https://yeachan-heo.github.io/oh-my-claudecode-website/docs/#cli-reference
- **에이전트 아키텍처**: `/omc-reference` 내에서 에이전트 카탈로그 확인 가능
- **상세 스킬별 가이드**: (각 스킬에 `/skill name` 형태로 접근하거나 `/omc-reference`에서 확인)

---

## 참고

- 플러그인 설치 후 반드시 `/reload-plugins` 실행
- 모호한 요청은 `/deep-interview` 또는 `/ralplan`으로 먼저 명확화
- 커맨드 전체 목록은 `/omc-reference`에서 확인 가능
