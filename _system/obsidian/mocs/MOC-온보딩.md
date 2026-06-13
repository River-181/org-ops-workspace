---
title: MOC 온보딩
kind: moc
area: ops
status: live
created: 2026-06-09
up: "[[MOC-운영]]"
tags:
  - type/moc
  - ops/onboarding
---

# MOC 온보딩

> [클럽명]에 새로 합류하는 사람·에이전트가 길을 잃지 않도록, 흩어진 온보딩 문서를 한 곳에서 잇는 허브.
> graphify 그래프 분석(2026-06-09)에서 온보딩 문서 3+갈래가 **상호참조 0**으로 파편화돼 있음이 드러나 신설.

## 1. 신규 멤버 (클럽 가입자)

| 단계                 | 문서                                                             |
| ------------------ | -------------------------------------------------------------- |
| Discord 첫 진입       | [[20260315-discord-onboarding-members-v_1\|Discord 멤버 온보딩]]    |
| 멤버 운영 DB·PKM 공간 생성 | `/member-onboard` 스킬 ([[member-onboard]])                      |
| 발행용 카피             | [[20260315-discord-onboarding-copy-pack-v_1\|Discord 온보딩 카피팩]] |

## 2. 운영팀 신규 (단계별 여정)

핵심 경로 — **Day 1 → Week 1 → Month 1** 순서로 따라간다:

| 시점 | 문서 | 목표 |
|------|------|------|
| 첫날 | [[온보딩-Lv1-Day1\|Lv1 · Day 1]] | 전체 지도·정신 모형 |
| 첫 주 | [[온보딩-Lv2-Week1\|Lv2 · Week 1]] | 주간 루틴·첫 실행 |
| 첫 달 | [[온보딩-Lv3-Month1\|Lv3 · Month 1]] | 세션 사이클 완주 |

병행 세팅:
- [[20260315-discord-onboarding-ops-v_1\|Discord 운영팀 온보딩]] — 역할·채널 권한
- [[260422-Syncthing-팀원온보딩\|Syncthing 팀원 온보딩]] — 볼트 P2P 동기화 연결
- [[260422-Syncthing-협업-인수인계\|Syncthing 협업 인수인계]] — 운영자 인수인계 절차

## 3. 에이전트·시스템 맥락 전승

에이전트가 부팅 시 사용자만큼 맥락을 갖도록 — 메모리OS가 세션·에이전트 간 이해를 물려준다.

- [[viz|메모리OS 시각화]] — 메모리 시스템 구조·흐름 (HTML 온보딩, `04-agent.html`은 에이전트 전용)
- [graphify 지식 그래프 대시보드](../../graphify-out/graph.html) — 볼트 전체 지형을 한눈에 (`/graphify query "<주제>"`로 도메인 즉시 파악)
- 작업 시작 전 `memory.recall(작업 주제)`로 의미 회상

## 관련

- 상위: [[MOC-운영]]
- 멤버 관리: [[MAP-멤버]]
- 플랫폼: [[Discord]] · [[MAP-플랫폼]]
