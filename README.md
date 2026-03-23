# org-ops-workspace

> 소규모 조직을 위한 로컬 퍼스트 운영 하네스
> Claude Code + Obsidian 기반 | 클럽·동아리·스타트업·팀플·학생회·하위팀 지원

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub Template](https://img.shields.io/badge/GitHub-Template-blue)](https://github.com/River-181/org-ops-workspace)

---

## What is this?

이 템플릿을 사용하는 조직이 얻는 것:

- **로컬 Obsidian 볼트 = 조직의 단일 진실 소스** — 모든 기획·기록·전략이 한 곳에
- **Claude Code 에이전트 팀** — 운영, 세션준비, 기록, 리서치, 발행을 AI가 협업
- **`/org-init` 한 번으로 조직 맞춤 설정** — 조직 유형, 이름, 운영 방식을 자동 반영
- **반복 작업 자동화** — 세션 폴더 생성, 주간 리뷰, 볼트 건강 체크, 메모리 관리

---

## Quick Start

### Path A — Claude Code 사용자 (권장)

```bash
# 1. GitHub에서 "Use this template" → 새 레포 생성
# 2. 로컬에 클론
git clone https://github.com/[your-org]/[your-repo].git
cd [your-repo]
# 3. Claude Code 실행
claude
# 4. 초기화 스킬 실행
/org-init
```

질문에 답하거나 기존 문서를 제공하면 자동으로 세팅이 완료됩니다.

### Path B — Obsidian 전용 사용자

```
1. GitHub에서 "Use this template" → 새 레포 생성 (또는 ZIP 다운로드)
2. Obsidian에서 볼트로 열기
3. _system/identity/SETUP-GUIDE.md 읽기
4. 순서대로 identity 파일 6개 작성
```

---

## `/org-init`이 하는 일

- 조직 유형 선택 (7가지: 클럽 / 동아리 / 스타트업 / 팀플 / 학생회 / 하위팀 / 기타)
- 기존 문서 업로드 또는 인터뷰로 조직 정보 수집
- `_system/identity/` 파일 6개 자동 생성 (조직명, 미션, 문화, 워크플로우, 플랫폼, 브랜드)
- `02_sessions/` 폴더 구조를 조직 방식에 맞게 설정 (세션 패턴: `S{NN}-{title}/` 또는 `{YYMMDD}-{title}/` 등)
- `CLAUDE.md` 조직 정보 반영 — 에이전트가 볼트 맥락을 바로 이해
- `active-context.md` 초기화 — 에이전트 메모리 준비 완료

---

## 지원 조직 유형

| 유형 | org_type | 특징 |
|------|----------|------|
| 취미/관심사 클럽 | `club` | 격주/매주, 장기 커뮤니티 |
| 대학 동아리 | `university-club` | 학기 기반, 임원 구조 |
| 스타트업 팀 | `startup` | 스프린트, 성과 중심 |
| 대학생 팀플 | `student-project` | 마감 기반, 단기 |
| 학생회 | `student-council` | 임기제, 공식 회의 |
| 하위팀 | `sub-team` | 상위 조직 종속 |
| 기타 | `other` | 자유 형식 |

---

## 폴더 구조

```
[your-org-workspace]/
├── _system/        에이전트 설정, 규칙, 정체성 파일
├── 01_ops/         운영 (전략, 회의, 모집, 재정, 멤버, 템플릿)
├── 02_sessions/    세션/활동 (기획, 자료, 기록)
├── 03_projects/    프로젝트
├── 04_studio/      브랜드 에셋, 콘텐츠
├── 05_library/     리서치, 발행물
└── 06_inbox/       분류 전 임시 파일
```

---

## 에이전트 팀

| 에이전트 | 역할 |
|---------|------|
| `@ops` | 주간 리뷰, 볼트 건강 체크, 작업 조율 |
| `@prep` | 세션 기획안, 자료 구성, 체크리스트 |
| `@record` | 세션 기록, 결정 로그, 회고 |
| `@research` | 방법론 연구, 도구 분석, 사례 조사 |
| `@publish` | 콘텐츠 제작, 공지, 플랫폼 발행 |
| `@session-team` | 세션 전체 파이프라인 조율 (리서치 → 준비 → 발행) |

에이전트 외에도 18개 스킬(`/org-init`, `/session-setup`, `/weekly-review`, `/vault-health` 등)이 제공됩니다.

---

## 요구사항

- **Claude Code** — Claude Code 사용자 (Path A)
- **Obsidian** — 권장, 없어도 기본 동작
- **Git**

---

## 기반 프레임워크

NUGI-Core(내우주기 클럽의 실제 운영 워크스페이스)에서 수개월간 검증된 프레임워크를 범용화한 템플릿입니다.
실제 운영에서 검증된 폴더 구조, 에이전트 아키텍처, 파일 네이밍 규칙을 기반으로 합니다.

---

## License

MIT
