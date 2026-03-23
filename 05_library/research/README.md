---
title: Research Registry
kind: moc
area: library
status: draft
created: 2026-03-23
up: "[[MOC-라이브러리]]"
tags:
  - type/moc
---

# 리서치 레지스트리

> 이 조직의 리서치 산출물 유형과 저장 위치를 정의한다.
> `@research` 에이전트가 이 파일을 참조해 산출물 위치를 결정한다.

## 산출물 유형별 저장 위치

| 유형 | 경로 | 설명 |
|------|------|------|
| 방법론 연구 | `05_library/research/methods/` | 운영 방법론, 프로세스 연구 |
| 사례 조사 | `05_library/research/references/` | 타 조직 사례, 외부 레퍼런스 |
| 컨셉 지도 | `05_library/research/methods/maps/` | 개념 연결 지도, 마인드맵 |
| AI 도구 연구 | `05_library/research/ai/` | AI 도구 분석, 활용 가이드 |

## 규칙

- 원본 자료 복사 금지 — `[[wikilink]]`로 연결
- 외부 URL은 본문에 삽입, 별도 파일 불필요
- 세션 준비용 리서치는 `02_sessions/` 폴더 내 `materials/`에 저장

## 관련 에이전트

- `@research` — 독립 탐구 및 세션 피드용 리서치
- `@research-team` — 병렬 다주제 리서치 조율
