---
title: Syncthing 협업 인수인계
kind: guide
area: ops
status: live
created: 2026-04-22
updated: 2026-04-22
up: "[[MOC-운영]]"
related:
  - "[[플랫폼 세팅 가이드]]"
  - "[[RUNBOOK — 볼트 유지]]"
tags:
  - ops/guide
  - ops/platform
  - ops/handoff
---

# Syncthing 협업 인수인계

> `[볼트명]`를 Syncthing로 2인 협업하기 위한 현재 확정 상태와 운영 규칙.
> 최초 연결 완료 기준 handoff.

---

## 1. 목적

이 문서는 관리자가 부재해도 다른 운영자나 팀원이 `[볼트명]` 동기화 상태를 복원하고 유지할 수 있도록 만든 최소 절차서다. 핵심은 세 가지다.

- 어떤 `Folder ID`가 현재 정답인지
- 팀원은 어떤 초대를 수락하고 어떤 것은 무시해야 하는지
- 어떤 파일은 Syncthing로 보내지 않도록 막아 두었는지

---

## 2. 현재 확정 상태

### 활성 공유 폴더

| 항목 | 값 |
|------|------|
| Label | `[클럽명]-workspace` |
| Folder ID | `[FOLDER-ID]` |
| Path | `[볼트경로]` |
| Folder Type | `Send & Receive` |
| Ignore File | `.stignore` (볼트 루트) |

### 연결된 기기

| 역할 | 이름 | Device ID |
|------|------|------|
| 관리자 로컬 | `[관리자-기기명]` | `[관리자-DEVICE-ID]` |
| 팀원 | `[팀원명]` | `[팀원-DEVICE-ID]` |

### 검증 당시 상태

- `syncthing cli show connections` 결과 `connected: true`
- 활성 폴더 정의는 `syncthing cli config folders [FOLDER-ID] dump-json` 로 확인
- 상대 장치 주소는 discovery에서 조회 가능했고 pending device/folder는 없었음

---

## 3. 팀원 세팅 절차

팀원은 아래 순서만 따르면 된다.

1. Syncthing를 실행한다.
2. `[클럽명]-workspace ([FOLDER-ID])` 초대만 `추가` 또는 `Accept` 한다.
3. 팀원 로컬 경로를 원하는 작업 폴더로 지정한다.
4. Folder Type은 `Send & Receive` 그대로 둔다.
5. 상태가 `Syncing` 후 `Up to Date` 로 바뀌는지 확인한다.

### 무시해야 하는 예전 초대

초기 연결 중 중복 공유가 생기면 오래된 Folder ID가 나타날 수 있다. 다시 보이면 수락하지 않는다.

원칙은 단순하다. **현재 정답은 `[FOLDER-ID]` 하나만 유지**하는 것이다.

---

## 4. 로컬 ignore 정책

Syncthing ignore는 `.stignore` (볼트 루트)에서 관리한다.

### 핵심 규칙

- `#include .gitignore`
- `.git`
- `.stfolder.removed-*`
- `.tmp.drivedownload/`
- `.trash/`
- `.ipynb_checkpoints/`
- `node_modules/`, `build/`, `dist/`, `.next/`, `.cache/`, `.turbo/`, `.venv/`, `.pytest_cache/`, `__pycache__/`

### 의미

- Git 이력은 Syncthing로 복제하지 않는다.
- 로컬 임시 파일과 복구 잔여물은 팀원에게 전파하지 않는다.
- 빌드 산출물과 캐시는 협업 대상이 아니라 재생성 대상이다.

새로운 생성 디렉터리가 자주 생기면 `.gitignore` 와 `.stignore`를 함께 갱신한다.

---

## 5. 런타임 주의사항

초기 설정 시 `Homebrew service` 와 `Syncthing.app` 인스턴스가 동시에 존재해 상태가 충돌할 수 있다.

### 증상

- `brew services list` 에서는 `syncthing error`
- 실제 프로세스는 앱 인스턴스에서 실행 중

### 운영 원칙

- **둘 중 하나만 사용**한다.
- 장기적으로는 아래 둘 중 하나로 정리한다.

1. `Syncthing.app` 를 `/Applications` 로 옮겨 앱 방식 유지
2. 앱 인스턴스를 완전히 종료한 뒤 `brew services start syncthing` 로 Homebrew 서비스로 전환

혼합 운영은 다시 충돌을 만든다.

---

## 6. 점검 명령

문제가 생기면 아래 명령부터 본다.

```bash
/opt/homebrew/opt/syncthing/bin/syncthing cli show connections
/opt/homebrew/opt/syncthing/bin/syncthing cli show discovery
/opt/homebrew/opt/syncthing/bin/syncthing cli show pending folders
/opt/homebrew/opt/syncthing/bin/syncthing cli config folders [FOLDER-ID] dump-json
```

### 정상 상태 판단

- `connections` 에서 팀원 장치가 `connected: true`
- `pending folders` 가 `{}` 또는 비어 있음
- 활성 폴더 ID가 `[FOLDER-ID]`

---

## 7. 다음 운영자에게

- 팀원이 새로 합류하면 먼저 **기기 추가**보다 **정답 Folder ID 하나 유지** 원칙을 설명한다.
- 중복 초대가 보이면 새로 만들지 말고 기존 활성 폴더 공유 설정을 먼저 확인한다.
- Git 협업과 Syncthing 협업은 별개다. Syncthing는 작업 파일 공유용이고, 변경 이력 관리는 Git으로 한다.
