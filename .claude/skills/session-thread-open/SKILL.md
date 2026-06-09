---
name: session-thread-open
description: 세션 당일 Discord 스레드에 개요·아젠다·준비물·자료 링크를 전송한다. session-ops launch D-0 체크리스트의 마지막 단계.
argument-hint: "[세션ID: S01|S02|...] [스레드ID]"
allowed-tools: Read, Bash, Edit
---

# session-thread-open

## 목적

세션 시작 직전, Discord 세션 스레드에 **당일 오프닝 메시지 1개**를 전송한다.  
멤버들이 스레드를 열면 바로 확인할 수 있는 정보 허브 역할.

---

## 입력

| 인자 | 필수 | 설명 |
|------|------|------|
| `세션ID` | 선택 | `S01`, `S02` 등. 없으면 active-context에서 파악 |
| `스레드ID` | 선택 | Discord 스레드 ID. 없으면 plan.md `discord_thread_id` 필드에서 읽음 |

---

## 전제 조건

- `_system/tools/.env` — `DISCORD_BOT_TOKEN` 존재
- 세션 plan.md — 날짜·시간·장소·아젠다 확정
- 세션 허브 `S{NN}-*.md` — `## 발표 / 자료` 섹션에 링크 정리됨

---

## 메시지 템플릿

```
**{세션ID} — {세션제목}**
오늘 {시간대} | {장소}

**아젠다**
{아젠다_항목들}

**준비물**
{준비물_항목들}

**자료**
{자료_항목들}
```

**규칙**:
- 이모지 없음
- 링크는 `항목명 → URL` 형식
- 아젠다: `{부} — {제목} ({담당} · {시간})`
- 준비물: `-` 불릿 리스트

---

## 절차

### 1. 컨텍스트 파악

```
_system/agents/memory/active-context.md
02_sessions/S{NN}-*/S{NN}-*-plan.md        ← 날짜·시간·장소·아젠다
02_sessions/S{NN}-*/S{NN}-*.md             ← 허브: ## 발표 / 자료 섹션
```

plan.md에서 추출:

| 필드 | 위치 |
|------|------|
| 세션 제목 | `title:` frontmatter 또는 `## 개요` 테이블 |
| 날짜·시간 | `## 개요` 테이블 |
| 장소 | `## 개요` 테이블 |
| 아젠다 | `## 구조` 또는 `## 아젠다` 섹션 |
| 준비물 | `## 준비물` 섹션 (체크박스 항목) |

허브 `## 발표 / 자료` 에서 추출:

| 필드 | 항목 |
|------|------|
| 슬라이드 URL | `1부 슬라이드 (주용) — 라이브` |
| 온보딩 URL | `Discord 온보딩 — 라이브` |
| 기타 자료 | 실습 자료, 2부 슬라이드 등 |

### 2. 스레드 ID 확인

우선순위:
1. 인자로 전달된 스레드 ID
2. plan.md frontmatter `discord_thread_id:` 필드
3. 사용자에게 직접 확인

### 3. 메시지 조합

템플릿에 추출한 값을 채운다.

**아젠다 형식 예시:**
```
1부 — AI 시대, 왜 지금인가 ([멤버명] · 45분)
2부 — 대학생활에서 AX 시작하기 ([멤버명] · 50분)
번외 — Discord 온보딩 (10분)
```

**준비물 형식 예시:**
```
- 노트북
- Discord 앱 설치 (이미 여기 계시면 완료)
- 사전 읽기: 없음
```

**자료 형식 예시:**
```
슬라이드 → https://[볼트명]-s01-ax-part-1.netlify.app
Discord 온보딩 가이드 → https://[볼트명]-s01-discord-onboarding.netlify.app
```

### 4. 사용자 확인

메시지 전문을 출력하고 전송 전 확인을 받는다:

```
아래 메시지를 스레드 {스레드ID}에 전송할까요?

─────────────────────────────
{메시지 전문}
─────────────────────────────

진행 / 수정사항 말씀해주세요.
```

### 5. Discord API 전송

> 함정 패턴 (User-Agent 필수, UTF-8, message_id): `_system/platform/Discord/260608-Discord-API-레시피.md` 참조.

```bash
source "_system/tools/.env"
export PYTHONUTF8=1

python3 - <<'PYEOF'
import json, pathlib

content = """[위 템플릿을 채운 메시지 전문]"""

pathlib.Path("/tmp/discord_payload.json").write_text(
    json.dumps({"content": content}, ensure_ascii=False),
    encoding="utf-8"
)
PYEOF

MESSAGE_ID=$(curl -s -X POST "https://discord.com/api/v10/channels/{스레드ID}/messages" \
  -H "Authorization: Bot $DISCORD_BOT_TOKEN" \
  -H "User-Agent: DiscordBot (https://[클럽명].club, 1.0)" \
  -H "Content-Type: application/json" \
  --data @/tmp/discord_payload.json \
  | python3 -c "import json,sys; print(json.load(sys.stdin)['id'])")

rm /tmp/discord_payload.json
echo "전송 완료. message_id: $MESSAGE_ID"
```

### 6. 전송 결과 기록

세션 허브 `S{NN}-*.md`의 `## 발표 / 자료` 섹션 아래에 추가:

```markdown
### 전송 기록

| 항목 | 값 |
|------|-----|
| 전송 시각 | YYYY-MM-DD HH:MM KST |
| 스레드 | {스레드ID} |
| 메시지 ID | {message_id} |
```

---

## 워크플로우 내 위치

```
session-ops setup          ← 폴더 생성
session-ops launch (D-1)   ← 준비 상태 점검
session-ops launch (D-0)   ← 당일 최종 체크
  └── session-thread-open  ← 스레드 오프닝 메시지 전송  ← 여기
세션 진행
session-ops record         ← 종료 후 기록
```

---

## 연관 스킬

| 스킬 | 관계 |
|------|------|
| `session-ops launch` | D-0 체크리스트 마지막 항목으로 호출 |
| `discord-announce` | 세션 전 `#announcements` 모집 공지 (이 스킬과 목적 다름) |
| `session-announce` | 포스터 + 공식 공지 (세션 전 홍보용, 이 스킬은 당일 오프닝) |
| `drop-publish` | Discord 전송 패턴 참고 (같은 API 사용) |

---

## 재사용 포인트

세션마다 달라지는 값만 plan.md와 허브에서 읽어오면 된다:
- `세션ID`, `세션제목`, `시간대`, `장소`, `아젠다`, `준비물`, `자료 URL들`

고정 값 (스킬 내부):
- 메시지 포맷 구조
- Discord API 엔드포인트
- 이모지 없음 / 깔끔한 구조 원칙
