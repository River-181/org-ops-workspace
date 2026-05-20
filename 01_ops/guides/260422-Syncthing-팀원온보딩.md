---
title: Syncthing 팀원 온보딩
kind: guide
area: ops
status: live
created: 2026-04-22
updated: 2026-04-22
up: "[[MOC-운영]]"
related:
  - "[[260422-Syncthing-협업-인수인계]]"
  - "[[syncthing]]"
  - "[[플랫폼 세팅 가이드]]"
tags:
  - ops/guide
  - ops/platform
  - ops/onboarding
---

# Syncthing 팀원 온보딩

> 신규 팀원이 자기 로컬 기기와 [볼트명] 워크스페이스를 Syncthing으로 연결하는 단계별 가이드.

---

## 1. 이 가이드가 목표하는 것

- Syncthing 설치
- 관리자 기기와 상호 인증
- 공식 공유 폴더 수신
- 로컬 Obsidian 볼트로 진입

---

## 2. 준비물

- 디스크 여유 최소 5GB (볼트 확장 대비)
- 관리자 Device ID: `[관리자-DEVICE-ID]` ← 관리자에게 직접 확인
- 관리자에게 **본인 Device ID**를 전달할 채널 확보 (Discord DM 권장)

---

## 3. OS별 설치

### macOS

**방법 1: Homebrew (권장)**

```bash
brew install syncthing
brew services start syncthing
```

**방법 2: 애플리케이션으로 설치**

https://syncthing.net/downloads/ 에서 `Syncthing.app` 다운로드 → `/Applications` 폴더로 이동 → 실행

> 주의: brew 서비스와 `.app`을 **동시에 실행하지 말 것**. 충돌 발생.

### Windows

1. https://syncthing.net/downloads/ 접속
2. Windows 인스톨러 다운로드 (`SyncTrayzor` 또는 공식 zip)
3. `SyncTrayzor` 설치 후 실행 → 시스템 트레이에 아이콘 등장
4. Web UI 자동 열림 (`http://127.0.0.1:8384`)

### Linux (Ubuntu/Debian)

```bash
sudo curl -o /etc/apt/keyrings/syncthing-archive-keyring.gpg https://syncthing.net/release-key.gpg
echo "deb [signed-by=/etc/apt/keyrings/syncthing-archive-keyring.gpg] https://apt.syncthing.net/ syncthing stable" | sudo tee /etc/apt/sources.list.d/syncthing.list
sudo apt update && sudo apt install syncthing
systemctl --user enable --now syncthing.service
```

---

## 4. 첫 실행 — 본인 Device ID 확인

1. Syncthing Web UI 접속: `http://127.0.0.1:8384`
2. 우상단 **Actions → Show ID** 클릭
3. 나타난 `XXXXXXX-XXXXXXX-...` (56자 문자열) 복사
4. 관리자에게 전달 (Discord DM)

---

## 5. 관리자 기기 추가

1. Web UI → **Add Remote Device**
2. Device ID 입력: `[관리자-DEVICE-ID]`
3. Device Name: `[관리자명]`
4. **Save**
5. 관리자 쪽에서 본인을 승인하면 연결됨

---

## 6. 공유 폴더 초대 수락

관리자가 폴더를 공유하면 Web UI에 **"New Folder Offered"** 알림이 뜬다.

1. **반드시 Folder ID `[FOLDER-ID]`인지 확인** (관리자에게 확인)
2. **Label**: `[클럽명]-workspace`
3. **Folder Path**: 자기 로컬 경로 지정
	- 예: `/Users/<username>/workspace/[클럽명]/[볼트명]`
	- Windows: `C:\Users\<username>\workspace\[클럽명]\[볼트명]`
4. **Folder Type**: `Send & Receive` 유지
5. **Save** → 상태가 `Syncing` → `Up to Date` 순으로 변함을 확인

> **경고**: 다른 Folder ID가 보이면 **수락하지 말 것**. 관리자에게 정답 ID를 확인할 것.

---

## 7. 동기화 확인

터미널에서:

```bash
syncthing cli show connections
```

출력에서 관리자 기기에 `connected: true`가 뜨면 성공.

Obsidian에서 해당 로컬 경로를 **Open folder as vault**로 열어 기존 파일들이 보이면 완료.

---

## 8. 트러블슈팅

| 증상 | 원인 후보 | 대응 |
|------|----------|------|
| 연결이 계속 `Disconnected` | 방화벽/NAT | Web UI → Settings → Connections → `Global Discovery`, `Relaying` 켜짐 확인 |
| 다른 Folder ID가 여러 개 보임 | 과거 중복 초대 | 관리자에게 정답 ID 확인 후 나머지는 무시/삭제 |
| 대용량 파일 동기화 안 됨 | `.stignore` 포함 | [[260422-Syncthing-협업-인수인계]] §4 참고 |
| 파일 작성 권한 오류 | 디렉터리 권한 | `chmod -R u+w <로컬경로>` |
| 충돌 파일 (`*.sync-conflict-*`) 발생 | 양쪽에서 동시 편집 | 최신본 선택 후 중복 삭제, 에디터 저장 타이밍을 서로 어긋나게 조정 |

---

## 9. 참고

- **운영자 handoff**: [[260422-Syncthing-협업-인수인계]]
- **툴 레지스트리**: [[syncthing]]
- **볼트 유지 RUNBOOK**: [[RUNBOOK-볼트유지]]
- **공식 문서**: https://docs.syncthing.net/
