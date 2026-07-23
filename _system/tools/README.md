---
title: 도구 레지스트리
kind: registry
area: system
status: live
created: 2026-03-24
updated: 2026-07-08
up: "[[MAP-도구]]"
tags:
  - system/tools
---

# 도구 레지스트리

> 이 볼트에서 사용하는 외부 CLI/MCP 도구 관리 허브.
> 각 도구의 설정·인증 데이터를 워크스페이스 안에서 통합 관리한다.
> Obsidian 탐색 허브: [[MAP-도구]]

---

## 환경변수

`.env` 파일 위치: `_system/tools/.env`

각 도구의 데이터 경로를 환경변수로 선언한다.
워크스페이스 루트에서 `source _system/tools/.env`로 활성화한다.

---

## 도구 목록

| 도구          | 설명                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | 인덱스 문서        | 데이터 경로          | Claude Code 스킬              | 환경변수                      |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- | --------------- | --------------------------- | ------------------------- |
| `feynman`   | Multi-agent research engine (research 하위 엔진 후보)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | [[feynman]]   | `feynman/`      | —                           | 없음                        |
| `figma`     | Figma MCP (원격 서버, OAuth)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | [[figma]]     | `figma/`        | `figma:figma-use` (플러그인)    | 없음 (OAuth 플러그인 관리)        |
| `kanban`    | 칸반 sync 스크립트 — frontmatter 동기화                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | [[Kanban]]    | `kanban/`       | —                           | 없음                        |
| `github`    | GitHub CLI (`gh`) — 릴리즈, 이슈 관리                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | [[GitHub]]    | `github/`       | —                           | 없음 (gh auth 관리)           |
| `syncthing` | 2인 이상 로컬 볼트 실시간 파일 동기화 데몬                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | [[syncthing]] | `syncthing/`    | —                           | 없음 (device ID 인증)         |
| `graphify`  | 볼트(.md) → 지식 그래프 **구조 질의** CLI. `graphify query "<질문>"`(BFS 탐색), `path "A" "B"`(최단경로), `explain "X"`(노드+이웃), `affected "X"`(역추적), 커뮤니티 탐지·허브노드. **메모리OS 역할 분담**: `memory.recall`(벡터 DB)은 유사도 기반 회상, graphify는 연결 구조("어떻게 이어지나") 전용 — 겹치는 부분은 graphify는 구조 질의에만 쓴다. **정식 등록(2026-06-08)** — 구조 질의 전용, `memory.recall`(유사도)과 병행 운용. 설치: `~/.local/bin/graphify` v0.8.36 (`uv tool install graphifyy`), 격리 설치. **그래프 빌드·증분 갱신은 `bash _system/tools/graphify/graph-update.sh`**(텍스트=DeepSeek/이미지=Haiku 하이브리드, `--images`로 이미지 비전). ⚠️ 한글 .md 의미추출은 LLM 비용 반복(매 갱신 ~$0.1 DeepSeek). 출력: `_system/graphify-out/`(파생물, .gitignore). **always-on 주입 금지 — on-demand 전용(`/graphify`)**. | [[graphify]]  | `graphify-out/` | `.claude/skills/graphify/`  | 없음                        |
| `ntn`       | Notion 공식 CLI (Beta) — 페이지·파일·DB를 터미널에서 직접 조작. `ntn pages`(Markdown 읽기·쓰기), `ntn files`(이미지·SVG·PDF·외부URL 업로드), `ntn api`(원시 API 호출). 인증 2트랙: `ntn api`/`ntn files`는 OAuth keychain(`ntn login`), `ntn pages`는 `NOTION_API_TOKEN`. 설치: `~/.local/bin/ntn` v0.14.2. | [[ntn]]       | `ntn/`          | —                           | `NOTION_API_TOKEN`          |
| `google-calendar` | Google Calendar — [클럽명] Club/Ops 공유 캘린더. 회원용은 세션·공개 모집·공개 마감, 운영팀용은 운영회의·준비 마감·예약 확인을 관리한다. Google OAuth 연결 전에는 `.ics` handoff로 운영하고, API 연결 후에는 운영자 승인 뒤 생성·수정한다. | [[google-calendar]] | `google-calendar/` | AI 사령탑 `google-workspace` | Google OAuth / `.ics` |
| `skillspector` | 외부 AI 에이전트 스킬 보안 스캐너 — 설치 전 **정적 분석**(64패턴/16카테고리: 프롬프트 인젝션·데이터 유출·권한상승 등) + 선택적 LLM 의미분석. `skillspector scan <경로\|URL\|zip>` (`--no-llm` 정적만, `--format terminal\|json\|markdown\|sarif`). 설치 v2.2.3 (격리 uv venv, `make install`). **폴더 전체 .gitignore/.stignore 제외**(머신 로컬 재설치). 사용 전 `source skillspector/.venv/bin/activate`. | — | `skillspector/` | — | 없음 |
| `im-not-ai` | `humanize-korean` 스킬 소스 (외부 `epoko77-ai/im-not-ai`). AI 티 한글 윤문 오케스트레이터 — 스킬 3종 + 에이전트 13종. **설치: 마켓플레이스 플러그인** `/plugin install humanize-korean@im-not-ai`(전역, `/reload-plugins`로 적용). skillspector 정적 스캔(오탐 전부) + 실행코드 수동 감사(stdlib-only·네트워크/exec/자격증명 0) 통과. **폴더 전체 .gitignore/.stignore 제외**(플러그인 소스 미러). | — | `im-not-ai/` | `humanize-korean@im-not-ai` (플러그인, 전역) | 없음 |
| `content-skills` | 영문 글쓰기 스킬 5종 소스 (외부 `artemnovitckii/content-skills`, MIT). `dumbify`(가독성 낮추기)·`storytelling`·`viral-hooks`·`anti-ai-writing`(AI 티 제거, 영문)·`voice-dna`(개인 보이스 프로필 빌드 가이드). **설치: SKILL.md 있는 4종을 `~/.claude/skills/`에 심링크**(`voice-dna`는 빌드 가이드라 폴더에 참고용으로만 보관). skillspector 정적 스캔(오탐 전부, 실행코드 0) + 수동 감사(네트워크/exec/인젝션 0) 통과. **폴더 전체 .gitignore/.stignore 제외**(스킬 소스 미러). ⚠️ 영문 대상 — 한글 윤문은 `humanize-korean` 사용. | — | `content-skills/` | `~/.claude/skills/{dumbify,storytelling,viral-hooks,anti-ai-writing}` (전역) | 없음 |

> **도구 추가 방법**
> 1. `_system/tools/<도구명>/` 폴더 생성 (런타임 데이터용)
> 2. 위 표에 행 추가
> 3. `.env.template`와 `.env`에 경로 환경변수 추가
> 4. `.gitignore`에 인증/바이너리 데이터 경로 추가
> 5. Claude Code 스킬이 있으면 `.claude/skills/<도구명>/`에 직접 저장
> 6. `tool` 노트 frontmatter에 `tool_id`, `tool_type`, `owner`, `auth_mode`, `verified_on`, `image`, `icon` 추가

---

---

## 각 도구 폴더 내부 구조 (표준)

새 도구를 추가할 때 아래 구조를 따른다.

```
_system/tools/<도구명>/
├── <도구명>.md                     ← Obsidian 인덱스/허브 (필수)
├── YYMMDD-<도구명>-운영가이드.md   ← 에이전트용 상세 운영 가이드 (권장)
│   - 설치 현황
│   - 환경 설정
│   - 검증된 워크플로우 (실전 테스트 결과 반영)
│   - 알려진 오류 & 해결법
│   - 결과물 저장 패턴
│   - 새 워크스페이스 복제 시 셋업
│
├── skill/                          ← Claude Code 스킬 (있는 경우)
│   ├── SKILL.md                    첫 섹션에 클럽 전용 설정 추가
│   └── references/
│
└── <인증·데이터 폴더>/              ← .gitignore로 제외
```

심볼릭 링크 패턴:
```bash
ln -s ../../_system/tools/<도구명>/skill .claude/skills/<도구명>
```

---

## Git 정책

- 설정 파일 (`config.toml`, `*.yaml`, `운영가이드.md`) — **추적**
- 인덱스 문서 (`<도구명>.md`) — **추적**
- 인증 데이터 (`profiles/`, `chrome-profiles/`, `auth.json`, `cookies`, `port-map.json`) — **제외**
- 바이너리/캐시 — **제외**

상세: 루트 `.gitignore`, 도구 전용 `_system/tools/.gitignore`

---

## 에이전트 Boot 시퀀스 (도구 사용 시)

어떤 에이전트든 도구를 사용하기 전:

1. `_system/tools/README.md` 읽기 — 도구 목록 확인
2. `_system/obsidian/maps/MAP-도구.md` 읽기 — 관련 허브/연결 문서 확인
3. `_system/tools/<도구명>/<도구명>.md` 읽기 — 도구 진입점 확인
4. `YYMMDD-<도구명>-운영가이드.md`가 있으면 읽기 — 운영 패턴 숙지
5. `source _system/tools/.env` 또는 direnv 확인
6. 스킬 파일이 있으면 해당 스킬의 **클럽 전용 섹션** 확인
