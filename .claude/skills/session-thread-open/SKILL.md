---
name: session-thread-open
description: 세션 당일 Discord 스레드에 개요·아젠다·준비물·자료 링크를 전송한다. session-ops launch D-0 체크리스트의 마지막 단계.
argument-hint: "[세션ID: S01|S02|...] [--thread 스레드ID]"
allowed-tools: Bash
---

# session-thread-open

## 목적

세션 시작 직전, Discord 세션 스레드에 **당일 오프닝 메시지 1개**를 전송한다.
내부 로직은 `_system/tools/send-session-thread.py`가 처리 — LLM 개입 없이 plan.md 파싱 + Discord API 직접 호출.

---

## 실행

```bash
# 1단계: 미리보기 (스레드ID 확인 + 메시지 검토)
python3 _system/tools/send-session-thread.py S03

# 스레드 ID가 있는 경우
python3 _system/tools/send-session-thread.py S03 --thread [Discord-채널-ID]

# 2단계: 사용자 확인 후 실제 전송
python3 _system/tools/send-session-thread.py S03 --thread [Discord-채널-ID] --send
```

스크립트가 자동으로:
1. `02_sessions/S{NN}-*/` 폴더 탐색
2. `*-plan.md`에서 개요(일시·장소)·아젠다·준비물 파싱
3. 허브 `S{NN}-*.md`의 `## 발표 / 자료` 섹션에서 자료 링크 추출
4. 메시지 조합 후 미리보기 출력
5. `--send` 플래그 시 Discord 스레드에 전송
6. plan.md에 전송 기록 저장 (discord_thread_id, message_id, 전송 시각)

---

## 사전 조건

- `_system/tools/.env` — `DISCORD_BOT_TOKEN`, `DISCORD_GUILD_ID` 존재
- 세션 plan.md — 일시·장소·아젠다·준비물 확정
- 스레드 ID — `--thread` 인자 또는 plan.md `discord_thread_id:` 필드

---

## 스레드 ID 확인

스레드 ID가 없으면 사용자에게 직접 확인한다:

```
스레드 ID가 없습니다. Discord에서 세션 스레드 ID를 확인해 --thread 인자로 전달해주세요.

예시: python3 _system/tools/send-session-thread.py S03 --thread [Discord-채널-ID] --send
```

---

## 메시지 포맷 (스크립트 자동 생성)

```
**{세션ID} — {세션제목}**
{일시} | {장소}

**아젠다**
{시간} {내용} ({비고})
...

**준비물**
- {항목}
...

**자료**
{항목명} → {URL}
...
```

규칙: 이모지 없음 / 링크는 `항목명 → URL` 형식

---

## 오류 처리

| 상황 | 동작 |
|------|------|
| 세션 폴더 없음 | 오류 메시지 + 종료 |
| plan.md 없음 | 오류 메시지 + 종료 |
| 스레드 ID 없음 | 미리보기는 표시, `--send` 시 오류 + 종료 |
| 봇 토큰 없음 | `--send` 시 오류 + 종료 |

---

## 스크립트 경로

`_system/tools/send-session-thread.py` — 수정이 필요하면 직접 편집.
