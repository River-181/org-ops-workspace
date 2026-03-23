---
description: 새 멤버 운영 DB 파일 생성
argument-hint: "[이름]"
---

# 멤버 온보딩

## 목적

새 멤버의 운영 프로필(`01_ops/members/`)을 생성하고,
볼트 이용 방법을 안내한다.

## 멤버 파일 구조 (두 레이어)

| 레이어 | 위치 | 성격 | 편집 권한 |
|--------|------|------|----------|
| 운영 DB | `01_ops/members/{이름}.md` | 역할·연락처·참여 추적 | 운영진만 |
| PKM 작업공간 | `05_library/members/{이름}/` | 개인 노트·실험 | 멤버 자유 |

## 절차

### 1. 신청 데이터 확인

해당 멤버의 기본 정보를 확인한다:
- 이름, 연락처, PKM 도구 경험, AI 사용 여부, IT 관심도, 관심사

데이터 위치는 조직에 맞게 설정 (예: 신청 폼 응답, 스프레드시트).

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
role: "[[Explorer]]"        # 조직 역할에 따라 변경
pkm_tools:
  - (조직에 맞게 설정)
ai_user: false
it_interest: 3              # 1–5 척도
interests: []
tags:
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
- 조직의 역할 체계에 따라 `role:` 필드 설정
- INDEX-MEMBER.md의 멤버 링크 목록에 추가

### 4. 세션 Backlink 확인
이미 진행된 세션 record.md의 `participants:`에 이름 추가.

### 5. PKM 작업공간 안내 (선택)
멤버가 볼트에 직접 접근하는 경우, `05_library/members/README.md`의
규칙을 공유하고 개인 폴더 생성 방법 안내.

## 주의사항

- 개인 연락처 등 민감 정보는 운영 DB에만 존재 — PKM 작업공간에 개인정보 기록 금지
- `participants:` 는 record.md에만 — plan.md에는 넣지 않는다
- 역할 변경 시 `role:` 필드와 `tags:` 모두 업데이트
