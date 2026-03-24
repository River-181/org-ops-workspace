---
name: member-onboard
description: 새 멤버 운영 DB 파일 생성 + PKM 작업공간 안내
argument-hint: "[이름]"
---

# 멤버 온보딩

## 목적

새 멤버의 운영 프로필(`01_ops/members/`)을 생성하고,
PKM 작업공간(`05_library/members/`) 이용 방법을 안내한다.

## 멤버 파일 구조 (두 레이어)

| 레이어 | 위치 | 성격 | 편집 권한 |
|--------|------|------|----------|
| 운영 DB | `01_ops/members/{이름}.md` | 역할·연락처·참여 추적 | 운영진만 |
| PKM 작업공간 | `05_library/members/{이름}/` | 개인 노트·실험 | 멤버 자유 |

## 절차

### 1. 신청폼 데이터 확인
`01_ops/members/2026031-신청폼응답.xlsx`에서 해당 멤버 행 확인:
- 이름, 학번, 전화번호, PKM 도구, AI 사용 여부, IT 관심도, 관심사

### 2. 운영 DB 파일 생성
`01_ops/templates/tpl-멤버프로필.md` 복사 → `01_ops/members/{이름}.md`

```yaml
---
title: {이름}
kind: member
area: ops
status: live
created: {오늘 날짜}
up: "[[INDEX-MEMBER]]"
role: "[[Explorer]]"        # Discord 역할에 따라 변경
season: S{N}
student_id: "{학번}"
phone: "{전화번호}"
pkm_tools:
  - 노션                    # 신청폼 응답 기반
ai_user: false              # 신청폼 응답 기반
it_interest: 3              # 1–5 척도, 신청폼 응답 기반
interests:
  - 정보/지식공유           # 신청폼 "하고싶은 것" 기반 정제
tags:
  - season/2026-S{N}
  - role/explorer
---
```

**Dataview 참여 이벤트 쿼리** (본문에 포함):
```dataview
LIST
FROM ""
WHERE contains(participants, [[{이름}]])
SORT file.ctime ASC
```
→ 멤버가 참석한 모든 세션·회의 record가 자동 집계된다.
→ 세션 종료 후 record.md에 `participants:` 추가하면 자동 반영.

### 3. 역할 배정
- Discord에서 역할 확인: `_system/platform/Discord/roles/`
- 운영진: `role: "[[Guide]]"`, `tag: role/guide`
- 일반: `role: "[[Explorer]]"`, `tag: role/explorer`
- INDEX-MEMBER.md의 멤버 링크 목록에 추가

### 4. 세션 Backlink 확인
이미 진행된 세션 record.md의 `participants:`에 이름 추가:
```bash
obsidian property:set name="participants" value="[...]" path="02_sessions/S{NN}-{제목}/record.md"
```

### 5. PKM 작업공간 안내 (선택)
멤버가 볼트에 직접 접근하는 경우, `05_library/members/README.md`의
규칙을 공유하고 개인 폴더 생성 방법 안내.

## 주의사항

- `phone:` 필드는 운영 DB에만 존재 — PKM 작업공간에 개인정보 기록 금지
- `participants:` 는 record.md에만 — plan.md에는 넣지 않는다
- 역할 변경 시 `role:` 필드와 `tags:` 모두 업데이트
