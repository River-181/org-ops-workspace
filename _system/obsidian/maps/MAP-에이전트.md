---
title: MAP-에이전트
kind: map
area: system
status: live
created: 2026-03-24
up: "[[MOC-시스템]]"
tags:
  - type/map
  - system/agent
---

# MAP-에이전트

> 에이전트 시스템 전체의 중간 허브.
> 팀 오케스트레이터 → 개별 에이전트 → 프로토콜 → 메모리 순으로 탐색한다.

---

## 에이전트 팀 (오케스트레이터)

복합 목적 파이프라인. 팀을 호출하면 서브에이전트를 디스패치한다.

| 팀 | 파이프라인 |
|----|-----------|
| `@session-team` | 리서치 → 내용 구성 → 자료 제작 → 배포 |
| `@research-team` | 병렬 다주제 리서치 → 통합 (독립 or 세션 피드) |
| `@studio-team` | 슬라이드 완성 → 공지 → 발행 |

---

## 개별 에이전트

### 조직 운영

| 에이전트 | 역할 |
|---------|------|
| `@ops` | 주간리뷰, 건강체크, 작업 조율 |
| `@prep` | 세션 기획안, 자료, 체크리스트 |
| `@record` | 세션 기록, 결정 로그, 회고 |
| `@research` | 방법론 연구, 도구 분석, 사례 조사 |
| `@publish` | 콘텐츠 제작, 공지, 플랫폼 발행 |

### 볼트 구조

| 에이전트 | 역할 |
|---------|------|
| `@architect` | 볼트 구조 총괄, Phase 판단 |
| `@graph-weaver` | 위키링크·up 연결, 고아 해소 |
| `@frontmatter-doctor` | frontmatter 진단·수정, 태그 정규화 |

---

## 프로토콜

- [[_system/agents/protocols/|에이전트 운영 프로토콜]]

---

## 메모리

- [[_system/agents/memory/active-context|현재 컨텍스트]]
- [[_system/agents/memory/change-log|변경 이력]]

---

## 자동 목록 (Dataview)

```dataview
TABLE title, status, kind
FROM "_system/agents"
WHERE file.name != "MAP-에이전트" AND file.name != "README"
SORT kind ASC, file.name ASC
```
