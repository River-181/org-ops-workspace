---
title: Tag Registry
kind: log
area: system
status: live
created: 2026-04-14
updated: 2026-07-06
up: "[[MAP-옵시디언]]"
---

# Tag Registry

> 볼트의 공식 태그 레지스트리.
> `tag-register` 스킬이 새 태그를 등록한다. `tag-audit` 스킬이 미등록 태그를 감지한다.
>
> - 새 태그: 해당 네임스페이스 섹션에 알파벳순 삽입
> - 네임스페이스 신규 추가: `update-governance` 스킬로 CLAUDE.md도 함께 갱신

---

## 승인된 네임스페이스

| 네임스페이스 | 용도 |
|------------|------|
| `ops/` | 운영 관련 |
| `session/` | 세션 관련 |
| `project/` | 프로젝트 관련 |
| `research/` | 리서치·연구 |
| `library/` | 라이브러리 분류 (000~900) |
| `platform/` | 플랫폼·도구 |
| `role/` | 역할·담당 |
| `season/` | 시즌 구분 |
| `studio/` | 스튜디오 산출물·브랜드 에셋 |
| `style/` | 비주얼 스타일 월드 |
| `type/` | 문서 유형 |
| `drop/` | Drop 콘텐츠 서브카테고리 |
| `system/` | 시스템·설정 |

---

## 예외

`04_studio` 사이드카 파일(`*.jpeg.md`, `*.webp.md` 등)의 `tags`는 코크핏 필터용 메타데이터로 볼트 태그 체계 적용 예외 — 감사에서 제외.

---

## drop/

| 태그 | 네임스페이스 | 설명 | 등록일 |
|------|------------|------|--------|
| `drop/AI` | drop/ | AI·LLM 관련 drop | 2026-04-22 |
| `drop/ai-geopolitics` | drop/ | AI 지정학·수출통제 drop | 2026-06-21 |
| `drop/ai-tools` | drop/ | AI·LLM 도구 drop | 2026-06-13 |
| `drop/career` | drop/ | 취업·인턴십 drop | 2026-06-21 |
| `drop/china` | drop/ | 중국 AI·테크 생태계 관련 drop | 2026-07-06 |
| `drop/infrastructure` | drop/ | AI 인프라·클라우드·데이터센터 관련 drop | 2026-07-02 |
| `drop/life` | drop/ | 라이프스타일 drop | 2026-06-21 |
| `drop/meta` | drop/ | Meta 관련 AI·플랫폼·시장 분석 drop | 2026-07-02 |
| `drop/pkm` | drop/ | PKM 관련 drop | 2026-06-21 |
| `drop/pm` | drop/ | Product Manager·제품 사고 관련 drop | 2026-07-06 |
| `drop/product` | drop/ | 제품 설계·제품 전략 관련 drop | 2026-07-06 |
| `drop/semiconductor` | drop/ | 반도체·AI칩·컴퓨팅 하드웨어 관련 drop | 2026-07-06 |
| `drop/stock-market` | drop/ | 주식시장·시가총액·시장 반응 기반 drop | 2026-07-02 |
| `drop/tips` | drop/ | 팁·노하우 drop | 2026-06-13 |
| `drop/Tooling` | drop/ | 개발 도구·CLI·MCP drop | 2026-04-22 |
| `drop/tools` | drop/ | 개발 도구·CLI drop | 2026-06-13 |
| `drop/vibe-coding` | drop/ | Vibe coding drop | 2026-06-13 |
| `drop/Workflow` | drop/ | 작업 흐름·방법론 drop | 2026-04-22 |

## library/

| 태그 | 네임스페이스 | 설명 | 등록일 |
|------|------------|------|--------|
| `library/articles` | library/ | 기사·아티클 분류 | 2026-06-21 |
| `library/audit` | library/ | 감사·진단 자료 분류 | 2026-06-21 |
| `library/map` | library/ | 지도·맵 분류 | 2026-06-21 |
| `library/system` | library/ | 시스템 관련 자료 분류 | 2026-06-21 |

## ops/

| 태그 | 네임스페이스 | 설명 | 등록일 |
|------|------------|------|--------|
| `ops/collaboration` | ops/ | 협업·공유 문서 | 2026-06-21 |
| `ops/dashboard` | ops/ | 대시보드·코크핏 문서 | 2026-06-21 |
| `ops/discord` | ops/ | Discord 관련 운영 문서 | 2026-06-21 |
| `ops/finance` | ops/ | 재정·예산·회비 문서 | 2026-06-21 |
| `ops/guide` | ops/ | 운영 가이드 문서 | 2026-06-13 |
| `ops/handoff` | ops/ | 핸드오프·인수인계 문서 | 2026-06-21 |
| `ops/history` | ops/ | 클럽 역사·연혁 문서 | 2026-06-21 |
| `ops/meeting` | ops/ | 운영 회의록 문서 | 2026-06-21 |
| `ops/member` | ops/ | 멤버 관련 문서 | 2026-06-21 |
| `ops/milestone` | ops/ | 마일스톤 기록 | 2026-06-21 |
| `ops/naming` | ops/ | 네이밍 규약 문서 | 2026-06-21 |
| `ops/onboarding` | ops/ | 운영팀 온보딩 문서 | 2026-06-21 |
| `ops/platform` | ops/ | 플랫폼 운영 설정 문서 | 2026-06-21 |
| `ops/post` | ops/ | 공지·포스트 | 2026-06-13 |
| `ops/protocol` | ops/ | 운영 프로토콜·규약 문서 | 2026-06-21 |
| `ops/recruit` | ops/ | 모집 관련 | 2026-06-13 |
| `ops/release` | ops/ | 릴리즈 공지 | 2026-06-13 |
| `ops/review` | ops/ | 운영 리뷰·브리핑 문서 | 2026-06-21 |
| `ops/routing` | ops/ | 라우팅·인덱스 문서 | 2026-06-21 |
| `ops/runbook` | ops/ | 운영 런북·유지 절차 | 2026-06-21 |
| `ops/security` | ops/ | 보안·접근 정책 문서 | 2026-06-21 |
| `ops/strategy` | ops/ | 운영 전략·비전 문서 | 2026-06-21 |
| `ops/templates` | ops/ | 운영 템플릿 | 2026-06-21 |
| `ops/timeline` | ops/ | 클럽 타임라인·연혁 | 2026-06-21 |
| `ops/update` | ops/ | 시스템 업데이트 공지 | 2026-06-21 |
| `ops/weekly-review` | ops/ | 주간 리뷰 문서 | 2026-04-14 |

## platform/

| 태그 | 네임스페이스 | 설명 | 등록일 |
|------|------------|------|--------|
| `platform/claude` | platform/ | Claude AI 플랫폼 관련 | 2026-06-13 |
| `platform/claude-code` | platform/ | Claude Code 도구 | 2026-06-13 |
| `platform/discord` | platform/ | Discord 플랫폼 관련 문서 | 2026-06-21 |
| `platform/figma` | platform/ | Figma 디자인 도구 | 2026-06-13 |
| `platform/notion` | platform/ | Notion 협업 플랫폼 | 2026-06-13 |
| `platform/obsidian` | platform/ | Obsidian 볼트·플러그인 관련 문서 | 2026-06-21 |

## project/

| 태그 | 네임스페이스 | 설명 | 등록일 |
|------|------------|------|--------|
| `project/active` | project/ | 진행 중인 프로젝트 | 2026-04-14 |

## research/

| 태그 | 네임스페이스 | 설명 | 등록일 |
|------|------------|------|--------|
| `research/ai` | research/ | AI·LLM 관련 연구 | 2026-06-21 |
| `research/dashboard` | research/ | 대시보드·시각화 연구 | 2026-06-21 |
| `research/filing` | research/ | 파일링·분류 체계 연구 | 2026-06-21 |
| `research/learning-methods` | research/ | 학습 방법론 연구 | 2026-06-13 |
| `research/library` | research/ | 라이브러리·컬렉션 연구 | 2026-06-21 |
| `research/methods` | research/ | 방법론 연구 | 2026-06-13 |
| `research/moc` | research/ | MOC·지식 지도 연구 | 2026-06-21 |
| `research/obsidian` | research/ | Obsidian 활용 연구 | 2026-06-21 |
| `research/organization` | research/ | 정보 조직화 연구 | 2026-06-21 |
| `research/pkm` | research/ | PKM 방법론 연구 | 2026-06-21 |
| `research/schedule` | research/ | 일정·스케줄 관리 연구 | 2026-06-21 |
| `research/time` | research/ | 시간 관리 연구 | 2026-06-21 |
| `research/tools` | research/ | 도구 분석·비교 연구 | 2026-06-21 |
| `research/writing` | research/ | 글쓰기·표현 연구 | 2026-06-21 |

## role/

| 태그 | 네임스페이스 | 설명 | 등록일 |
|------|------------|------|--------|
| `role/archivist` | role/ | 아키비스트 역할 | 2026-06-21 |
| `role/connector` | role/ | 커넥터 역할 | 2026-06-21 |
| `role/curator` | role/ | 큐레이터 역할 | 2026-06-21 |
| `role/explorer` | role/ | 탐험자 역할 | 2026-06-13 |
| `role/guide` | role/ | 가이드 역할 | 2026-06-21 |
| `role/ops` | role/ | 운영자 역할 | 2026-06-21 |

## season/

| 태그 | 네임스페이스 | 설명 | 등록일 |
|------|------------|------|--------|
| `season/[YYYY-SN]` | season/ | 시즌 구분 태그 (예: `season/2026-S1`) | — |

## session/

| 태그 | 네임스페이스 | 설명 | 등록일 |
|------|------------|------|--------|
| `session/adhoc` | session/ | 비정기 애드혹 세션 | 2026-06-21 |
| `session/meta` | session/ | 세션 메타·운영 문서 | 2026-06-21 |
| `session/plan` | session/ | 세션 기획안 | 2026-04-14 |
| `session/prep` | session/ | 세션 준비 자료 | 2026-06-21 |
| `session/record` | session/ | 세션 기록 | 2026-04-14 |
| `session/S00` | session/ | S00 세션 관련 파일 | 2026-06-21 |
| `session/S01` | session/ | S01 세션 관련 파일 | 2026-06-13 |
| `session/S02` | session/ | S02 세션 관련 파일 | 2026-06-21 |

## studio/

> 신설 네임스페이스 — 스튜디오 산출물·브랜드 에셋 분류

| 태그 | 네임스페이스 | 설명 | 등록일 |
|------|------------|------|--------|
| `studio/brand` | studio/ | 브랜드 관련 산출물 | 2026-06-13 |
| `studio/character` | studio/ | 캐릭터 디자인 산출물 | 2026-06-13 |
| `studio/excalidraw` | studio/ | Excalidraw 템플릿·다이어그램 산출물 | 2026-06-21 |
| `studio/fonts` | studio/ | 스튜디오 폰트·타이포그래피 에셋 | 2026-07-02 |
| `studio/lego` | studio/ | 레고 스타일 산출물·프롬프트 | 2026-06-21 |
| `studio/poster` | studio/ | 스튜디오 포스터 산출물 | 2026-06-13 |
| `studio/template` | studio/ | 스튜디오 재사용 템플릿 | 2026-06-21 |

## style/

> 신설 네임스페이스 — 비주얼 스타일 월드 분류

| 태그 | 네임스페이스 | 설명 | 등록일 |
|------|------------|------|--------|
| `style/B-시네마틱토이포토` | style/ | Style B — 시네마틱 토이포토 비주얼 | 2026-06-13 |
| `style/F-브릭모자이크` | style/ | Style F — 브릭 모자이크 비주얼 | 2026-06-13 |
| `style/G-등축분해도` | style/ | Style G — 등축 분해도 블루프린트 비주얼 | 2026-06-13 |
| `style/H-시스템파라미터` | style/ | Style H — 시스템 파라미터 비주얼 | 2026-06-13 |

## system/

| 태그 | 네임스페이스 | 설명 | 등록일 |
|------|------------|------|--------|
| `system/agents` | system/ | 에이전트 설정·프로토콜 문서 | 2026-06-21 |
| `system/automation` | system/ | 자동화·스케줄 설정 문서 | 2026-06-21 |
| `system/audit` | system/ | 감사·진단 문서 | 2026-06-13 |
| `system/ax` | system/ | AX(AI 전환) 프로그램 관련 문서 | 2026-06-21 |
| `system/base` | system/ | Obsidian Bases 설정 문서 | 2026-06-21 |
| `system/config` | system/ | 시스템 설정 문서 | 2026-04-14 |
| `system/dataview` | system/ | Dataview 쿼리 문서 | 2026-06-21 |
| `system/decision` | system/ | 의사결정 기록 문서 | 2026-06-21 |
| `system/design` | system/ | 시스템 설계·아키텍처 문서 | 2026-06-21 |
| `system/health` | system/ | 볼트 건강·점검 문서 | 2026-06-21 |
| `system/identity` | system/ | 클럽 정체성·아이덴티티 문서 | 2026-04-22 |
| `system/index` | system/ | 인덱스·맵 문서 | 2026-06-21 |
| `system/obsidian` | system/ | 옵시디언 관련 설정 및 디자인 | 2026-06-13 |
| `system/platform` | system/ | 플랫폼 설정 문서 | 2026-06-21 |
| `system/reference` | system/ | 참고자료 저장소 문서 | 2026-06-13 |
| `system/schema` | system/ | 볼트 구조·규칙 정의 문서 | 2026-06-21 |
| `system/security` | system/ | 보안 점검·리뷰 문서 | 2026-06-21 |
| `system/skills` | system/ | 스킬 정의 문서 | 2026-06-13 |
| `system/tools` | system/ | 도구·유틸리티 설정 | 2026-06-13 |
| `system/upgrade` | system/ | 업그레이드 설계 문서 | 2026-06-21 |

## type/

| 태그 | 네임스페이스 | 설명 | 등록일 |
|------|------------|------|--------|
| `type/activity` | type/ | 활동 기록 문서 | 2026-06-21 |
| `type/audit` | type/ | 감사·진단 문서 | 2026-06-21 |
| `type/brief` | type/ | 브리프 문서 | 2026-06-13 |
| `type/decision` | type/ | 의사결정 로그 문서 | 2026-06-21 |
| `type/design` | type/ | 설계 문서 | 2026-06-21 |
| `type/design-spec` | type/ | 디자인 스펙 문서 | 2026-06-13 |
| `type/drop` | type/ | Drop 콘텐츠 | 2026-06-13 |
| `type/guide` | type/ | 가이드·튜토리얼 문서 | 2026-06-13 |
| `type/handout` | type/ | 핸드아웃·배포 자료 | 2026-06-21 |
| `type/index` | type/ | 인덱스 문서 | 2026-06-21 |
| `type/learning` | type/ | 학습 자료 | 2026-06-21 |
| `type/lecture` | type/ | 강의·발표 자료 | 2026-06-21 |
| `type/log` | type/ | 로그·기록 문서 | 2026-04-14 |
| `type/map` | type/ | MAP 동적 탐색 허브 문서 | 2026-06-21 |
| `type/moc` | type/ | MOC 문서 | 2026-06-13 |
| `type/note` | type/ | 일반 노트 | 2026-06-21 |
| `type/ops` | type/ | 운영 문서 | 2026-06-21 |
| `type/plan` | type/ | 기획·계획 문서 | 2026-06-21 |
| `type/poster` | type/ | 포스터 디자인 파일 | 2026-06-21 |
| `type/progress` | type/ | 진행 상황 추적 문서 | 2026-06-21 |
| `type/prompt` | type/ | 이미지·AI 프롬프트 문서 | 2026-06-21 |
| `type/reference` | type/ | 참고 문서 | 2026-06-21 |
| `type/research` | type/ | 연구 노트 | 2026-06-21 |
| `type/script` | type/ | 스크립트·코드 문서 | 2026-06-21 |
| `type/template` | type/ | 템플릿 파일 | 2026-04-14 |
| `type/workflow` | type/ | 워크플로우 문서 | 2026-06-13 |
