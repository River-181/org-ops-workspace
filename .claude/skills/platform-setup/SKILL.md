---
name: platform-setup
description: Discord 봇과 Notion API 초기 연결 — 단계별 안내로 .env 생성 및 연결 테스트
argument-hint: "[discord|notion|all — 생략 시 대화형]"
allowed-tools: Read, Write, Edit, Bash
---

# /platform-setup — 플랫폼 연결 초기 세팅

이 스킬은 볼트와 외부 플랫폼(Discord, Notion)을 연결하는 초기 설정을 AI가 단계별로 안내한다.
설정값은 `_system/tools/.env`에 저장되고, Drop 발행·공지 발행 등 자동화 스킬이 이 파일을 참조한다.

---

## Step 0: 현재 상태 확인

`_system/tools/.env`를 읽는다. 파일이 없으면 신규 설정으로 진행한다.

존재하면 현재 설정 상태를 표시한다:

```
현재 설정 상태:

Discord:
  BOT_TOKEN    ✅ 설정됨  /  ❌ 미설정
  GUILD_ID     ✅ 설정됨  /  ❌ 미설정
  SHARE_CHANNEL_ID  ✅ 설정됨  /  ❌ 미설정

Notion:
  API_KEY         ✅ 설정됨  /  ❌ 미설정
  DROPS_DB_ID     ✅ 설정됨  /  ❌ 미설정

어떤 플랫폼을 설정하시겠습니까?
  1. Discord 설정 (또는 업데이트)
  2. Notion 설정 (또는 업데이트)
  3. 전체 설정
  4. 연결 테스트만
```

인자가 있으면 해당 플랫폼으로 바로 진행한다.

---

## Discord 설정 절차

### D-1: Discord 봇 생성

사용자에게 안내한다:

```
Discord 봇을 생성해야 합니다.

1. 아래 주소를 브라우저에서 여세요:
   https://discord.com/developers/applications

2. 오른쪽 위 [New Application] 클릭
3. 봇 이름 입력 (예: [클럽명]-bot) → [Create]
4. 왼쪽 메뉴에서 [Bot] 선택
5. [Reset Token] → [Yes, do it] → 토큰 복사
   ⚠️ 토큰은 한 번만 표시됩니다. 지금 바로 복사하세요.

봇 토큰을 여기에 붙여넣으세요:
```

입력받은 값을 `DISCORD_BOT_TOKEN`으로 저장한다.

### D-2: 봇 권한 설정 및 서버 초대

```
봇을 서버에 초대합니다.

1. 왼쪽 메뉴 [OAuth2] → [URL Generator]
2. SCOPES에서 [bot] 체크
3. BOT PERMISSIONS에서 다음 권한 체크:
   ✅ Send Messages
   ✅ Read Message History
   ✅ View Channels
4. 페이지 하단의 Generated URL 복사 → 브라우저에서 열기
5. 봇을 추가할 서버 선택 → [Authorize]

초대가 완료되면 엔터를 눌러주세요.
```

### D-3: 서버 ID (Guild ID) 가져오기

```
Discord 서버 ID를 가져옵니다.

1. Discord 앱에서 설정(⚙️) → 고급 → [개발자 모드] 활성화
2. 좌측 서버 아이콘을 우클릭 → [서버 ID 복사]

서버 ID를 붙여넣으세요:
```

입력받은 값을 `DISCORD_GUILD_ID`로 저장한다.

### D-4: 공유 채널 ID 가져오기

```
Drop을 발행할 채널 ID를 가져옵니다.

1. Discord에서 해당 채널을 우클릭
2. [채널 ID 복사] 클릭

채널 이름과 ID를 알려주세요.
예: #share → 1234567890123456789

채널 ID:
```

입력받은 값을 `DISCORD_SHARE_CHANNEL_ID`로 저장한다.

### D-5: 운영자 Discord ID 등록

```
Drop 발행 시 작성자 멘션(@태그)에 사용합니다.
등록하지 않으면 멘션 없이 발행됩니다.

운영자 이름과 Discord 사용자 ID를 입력해주세요.
Discord 사용자 ID 가져오는 법:
  1. Discord에서 해당 사용자 우클릭
  2. [사용자 ID 복사]

형식: 이름,ID (한 줄에 한 명. 완료되면 빈 줄 입력)
예:
  홍길동,123456789012345678
  김철수,987654321098765432
  (빈 줄로 완료)
```

입력받은 목록을 `DISCORD_USER_{이름}` 형식으로 저장한다.

### D-6: Discord 연결 테스트

```bash
source _system/tools/.env

curl -s -H "Authorization: Bot $DISCORD_BOT_TOKEN" \
  "https://discord.com/api/v10/users/@me" | python3 -c "
import json, sys
data = json.load(sys.stdin)
if 'username' in data:
    print(f'✅ Discord 연결 성공: @{data[\"username\"]}')
else:
    print(f'❌ 연결 실패: {data}')
"
```

실패 시 토큰을 다시 확인하도록 안내한다.

---

## Notion 설정 절차

### N-1: Notion 통합 생성

```
Notion API 통합을 생성합니다.

1. 아래 주소를 브라우저에서 여세요:
   https://www.notion.so/my-integrations

2. [+ New integration] 클릭
3. 이름 입력 (예: [클럽명] Workspace Bot)
4. 워크스페이스 선택
5. [Submit] → [Show] → API 키(Internal Integration Secret) 복사
   ⚠️ 'secret_'으로 시작하는 긴 문자열입니다.

API 키를 붙여넣으세요:
```

입력받은 값을 `NOTION_API_KEY`로 저장한다.

### N-2: Drops DB 공유

```
Notion의 Drops 데이터베이스에 통합을 연결합니다.

1. Notion에서 Drops DB 페이지를 엽니다
2. 오른쪽 위 [...] (더보기) → [Connections] (또는 [Add connections])
3. 방금 만든 통합 이름 검색 → 선택

Drops DB URL을 붙여넣으세요:
예: https://notion.so/yourworkspace/abc123def456...?v=...

URL에서 DB ID를 자동으로 추출합니다.
```

URL에서 DB ID를 추출한다:
```python
import re
url = "{사용자 입력 URL}"
match = re.search(r'([a-f0-9]{32})', url.replace('-', ''))
db_id = match.group(1) if match else None
# 하이픈 형식으로 변환: 8-4-4-4-12
if db_id:
    formatted = f"{db_id[:8]}-{db_id[8:12]}-{db_id[12:16]}-{db_id[16:20]}-{db_id[20:]}"
```

추출된 ID를 `NOTION_DROPS_DB_ID`로 저장한다.

### N-3: Notion 연결 테스트

```bash
source _system/tools/.env

curl -s -X GET "https://api.notion.com/v1/users/me" \
  -H "Authorization: Bearer $NOTION_API_KEY" \
  -H "Notion-Version: 2022-06-28" | python3 -c "
import json, sys
data = json.load(sys.stdin)
if 'name' in data:
    print(f'✅ Notion 연결 성공: {data[\"name\"]}')
else:
    print(f'❌ 연결 실패: {data.get(\"message\", data)}')
"
```

DB 접근 테스트:
```bash
curl -s -X GET "https://api.notion.com/v1/databases/$NOTION_DROPS_DB_ID" \
  -H "Authorization: Bearer $NOTION_API_KEY" \
  -H "Notion-Version: 2022-06-28" | python3 -c "
import json, sys
data = json.load(sys.stdin)
if 'title' in data:
    title = ''.join(t['plain_text'] for t in data['title'])
    print(f'✅ DB 접근 성공: {title}')
else:
    print(f'❌ DB 접근 실패: {data.get(\"message\", data)}')
    print('DB에 통합이 연결되어 있는지 확인하세요 (N-2 단계)')
"
```

---

## Step FINAL: .env 파일 작성

수집한 모든 값으로 `_system/tools/.env`를 작성한다.

`.env.template`을 기반으로, 수집한 값을 채워 넣는다:

```bash
# [클럽명] 워크스페이스 도구 환경변수
# 사용법: source _system/tools/.env (워크스페이스 루트에서 실행)
# 생성일: {오늘 날짜}
# /platform-setup으로 자동 생성됨

# ── DISCORD ──────────────────────────────────────────────────────────
export DISCORD_BOT_TOKEN={수집된 값}
export DISCORD_TOKEN=$DISCORD_BOT_TOKEN
export DISCORD_GUILD_ID={수집된 값}
export DISCORD_SHARE_CHANNEL_ID={수집된 값}

# ── DISCORD 운영자 ID ─────────────────────────────────────────────────
{운영자별 export DISCORD_USER_{이름}={ID} 라인들}

# ── NOTION ────────────────────────────────────────────────────────────
export NOTION_API_KEY={수집된 값}
export NOTION_DROPS_DB_ID={수집된 값}
```

파일 작성 후:

```
✅ _system/tools/.env 저장 완료

설정된 플랫폼:
  Discord: @{봇 이름} (서버: {guild_id})
  Notion:  {DB 이름} 연결됨

이제 사용 가능한 스킬:
  /drop-publish    — Drop을 Discord + Notion에 동시 발행
  /discord-announce — 채널별 공지 초안 작성

추가 설정이 필요하면 /platform-setup을 다시 실행하세요.
```

---

## 도구 레지스트리 업데이트

`_system/tools/README.md`의 도구 목록 표에 항목을 추가한다:

```markdown
| Discord Bot | Discord 메시지 자동 발행 | — | `/drop-publish`, `/discord-announce` | `DISCORD_BOT_TOKEN`, `DISCORD_GUILD_ID`, `DISCORD_SHARE_CHANNEL_ID`, `DISCORD_USER_*` |
| Notion API | Drops DB 자동 발행 | — | `/drop-publish` | `NOTION_API_KEY`, `NOTION_DROPS_DB_ID` |
```

---

## 오류 처리

| 상황 | 대응 |
|------|------|
| 봇 토큰 유효하지 않음 | D-1 단계부터 다시 안내 |
| 봇이 서버에 없음 | D-2 초대 절차 재안내 |
| Notion 통합 권한 없음 | N-2 공유 절차 재안내 (DB에 연결됐는지 확인) |
| DB ID 추출 실패 | URL 형식 예시 제공 후 직접 입력 요청 |

---

## 규칙

- 토큰·API 키 입력 시 값을 대화창에 에코(echo)하지 않는다 — 파일에만 저장
- `.env` 파일은 `.gitignore`에 포함되어야 한다 (확인 필요 시 Bash로 검증)
- 기존 설정이 있으면 덮어쓰기 전 확인을 받는다
- 테스트 실패 시 다음 단계로 넘어가지 않는다
