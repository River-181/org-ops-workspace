---
name: drop-publish
description: 현재 Drop 파일을 Discord #share + Notion Drops DB에 발행하고 drops.csv를 갱신한다
argument-hint: "[파일경로 — 생략 시 현재 파일]"
allowed-tools: Read, Edit, Bash, mcp__claude_ai_Notion__notion-create-pages
---

# /drop-publish — Drop 발행 스킬

## 목적

`drop_status: ready` 상태의 Drop 파일을 Discord와 Notion에 동시 발행한다.
멱등성 보장: 이미 발행된 단계는 재실행해도 skip.

---

## 환경 확인

실행 전 반드시:

```bash
source _system/tools/.env
echo "봇 토큰: ${DISCORD_BOT_TOKEN:0:10}..."
echo "채널 ID: $DISCORD_SHARE_CHANNEL_ID"
```

토큰이 비어 있으면 `_system/tools/.env` 확인 후 `source` 재실행.

---

## Step 1: 파일 읽기 & 유효성 검사

현재 Drop 파일의 frontmatter를 읽는다.

**검사 항목**:
- `drop_status: ready` 인지 확인 → 아니면 중단하고 사용자에게 알림
- `drop_number` 존재 & 4자리 zero-pad 형식인지 확인
- `title` 비어 있지 않은지 확인
- `author` 필드 읽기 (없으면 멘션 생략)

**본문 파싱**:
- `## 공유 내용` ~ `## 링크` 사이 텍스트 = Discord 메시지 본문
- `## 링크` 섹션의 URL들 = 메시지 끝에 추가
- `---` 이후 `>` 블록 = 내부 메모 → **제외**

---

## Step 2: drop_number 중복 검사

```bash
# {drop_number}를 frontmatter의 실제 4자리 zero-pad 값으로 대체
grep "^{실제_drop_number}," 04_studio/drops/drops.csv
```

**중요**: `{drop_number}`는 frontmatter에서 읽은 4자리 zero-pad 문자열로 대체.
숫자 `16`이 아닌 문자열 `0016`으로 비교해야 정확.
이미 존재하면 중단하고 사용자에게 알림.

---

## Step 3: Discord 발행

`discord_message_id`가 이미 있으면 이 단계를 **skip**.

### 3-1. 메인 메시지 — 카카오톡 공유용 섹션 사용

Discord #share 채널에 올라가는 메인 메시지는 **`## 카카오톡 공유용`** 코드블럭 안의 텍스트다. `## 공유 내용`이 아님에 주의.

```python
import re
with open(drop_file, 'r') as f:
    content = f.read()
match = re.search(r'## 카카오톡 공유용\s*\n```\n(.*?)\n```', content, re.DOTALL)
kakao_text = match.group(1).strip() if match else ""
```

### 3-2. 스레드 생성 기준

공유 내용의 규모에 따라 스레드 생성 여부를 결정한다:

| 조건 | 처리 |
|------|------|
| `## 공유 내용`에 이미지(`![[...]]`)가 있거나 섹션이 2개 이상 | 스레드 생성 후 전문 발행 |
| `## 공유 내용`이 짧고(500자 이하) 이미지 없음 | 스레드 없이 #share에만 게시 |

**원칙**: 핵심 요약은 #share에, 상세 내용·이미지·코드는 스레드에 분리해 팀원들이 Discord를 정리된 커뮤니티로 사용할 수 있게 한다.

### 3-3. 메인 메시지 API 호출

> 함정 패턴 (User-Agent 필수, UTF-8, message_id): `_system/platform/Discord/260608-Discord-API-레시피.md` 참조.

```bash
source _system/tools/.env
export PYTHONUTF8=1

# kakao_text는 Step 3-1에서 추출 — python3으로 payload 파일 생성
python3 - <<'PYEOF'
import json, pathlib

kakao_text = """[Step 3-1에서 추출한 카카오톡 공유용 텍스트]"""

pathlib.Path("/tmp/discord_drop.json").write_text(
    json.dumps({"content": kakao_text}, ensure_ascii=False), encoding="utf-8"
)
PYEOF

MESSAGE_ID=$(curl -s -X POST \
  "https://discord.com/api/v10/channels/$DISCORD_SHARE_CHANNEL_ID/messages" \
  -H "Authorization: Bot $DISCORD_BOT_TOKEN" \
  -H "User-Agent: DiscordBot (https://[클럽명].club, 1.0)" \
  -H "Content-Type: application/json" \
  --data @/tmp/discord_drop.json \
  | python3 -c "import json,sys; print(json.load(sys.stdin)['id'])")

rm /tmp/discord_drop.json
echo "Discord message ID: $MESSAGE_ID"
```

**성공**: `id` 필드가 있는 JSON 응답
**실패**: `code` 필드가 있는 에러 → 에러 내용 출력 후 **중단** (Notion 진행 안 함)

### 3-4. 스레드 생성 (조건 충족 시)

```bash
THREAD_ID=$(curl -s -X POST \
  "https://discord.com/api/v10/channels/$DISCORD_SHARE_CHANNEL_ID/messages/$MESSAGE_ID/threads" \
  -H "Authorization: Bot $DISCORD_BOT_TOKEN" \
  -H "User-Agent: DiscordBot (https://[클럽명].club, 1.0)" \
  -H "Content-Type: application/json" \
  -d "{\"name\": \"${제목} 전문\"}" \
  | python3 -c "import json,sys; print(json.load(sys.stdin)['id'])")
echo "Thread ID: $THREAD_ID"
```

### 3-5. 스레드 내 섹션 발행

`## 공유 내용`의 섹션별로 텍스트+이미지를 순서대로 발행한다. 이미지는 `04_studio/assets/etc/`에서 찾는다.

```bash
# 텍스트 + 이미지 함께 발행 (multipart)
curl -s -X POST \
  "https://discord.com/api/v10/channels/$THREAD_ID/messages" \
  -H "Authorization: Bot $DISCORD_BOT_TOKEN" \
  -H "User-Agent: DiscordBot (https://[클럽명].club, 1.0)" \
  -F "payload_json=$(python3 -c "import json; print(json.dumps({'content': '섹션 텍스트'}))")" \
  -F "files[0]=@04_studio/assets/etc/이미지파일.png;type=image/png"

# 텍스트만 발행
curl -s -X POST \
  "https://discord.com/api/v10/channels/$THREAD_ID/messages" \
  -H "Authorization: Bot $DISCORD_BOT_TOKEN" \
  -H "User-Agent: DiscordBot (https://[클럽명].club, 1.0)" \
  -H "Content-Type: application/json" \
  -d "$(python3 -c "import json; print(json.dumps({'content': '섹션 텍스트'}))")"
```

**MIME 타입**: `.png` → `image/png`, `.jpg/.jpeg` → `image/jpeg`, `.gif` → `image/gif`, `.mov` → `video/quicktime`

---

## Step 4: Notion 발행

`notion_page_id`가 이미 있으면 이 단계를 **skip**.

### 4-1. Notion MCP 시도

`notion-create-pages` 도구 사용:

```
parent_id: {NOTION_DROPS_DB_ID — 환경변수에서 로드: $NOTION_DROPS_DB_ID}
properties:
  Drop (title): "{title}"
  Type (select): "{type}"
  Source (text): "{source}"
  Link (url): "{link}"
  Memo (text): "{memo}"
  Visibility (select): "{visibility}"
  Date (date): "{published_at 또는 오늘 날짜}"

page body (children):
  paragraph: "## 공유 내용\n\n{본문 텍스트}"
```

**성공**: page_id 포함 응답 → Step 4 완료
**실패 (Cloudflare 차단 등)**: 경고 출력 후 → **4-2 폴백 실행**

### 4-2. Notion CLI (`ntn`) 폴백

MCP가 실패하면 `ntn`으로 직접 API 호출한다. `ntn`은 시스템 키체인 인증을 사용하므로 별도 토큰 불필요.

```python
import json, subprocess

payload = {
    "parent": {"database_id": "{NOTION_DROPS_DB_ID}"},
    "properties": {
        "Drop": {"title": [{"text": {"content": "{title}"}}]},
        "Type": {"select": {"name": "{type}"}},
        "Source": {"rich_text": [{"text": {"content": "{source}"}}]},
        "Link": {"url": "{link}"},
        "Memo": {"rich_text": [{"text": {"content": "{memo}"}}]},
        "Visibility": {"select": {"name": "{visibility}"}},
        "Date": {"date": {"start": "{published_at}"}}
    },
    "children": [
        # heading_2: 공유 내용
        # 섹션별 paragraph / code / bulleted_list_item 블록
        # heading_2: 링크
        # 링크 paragraph 블록
    ]
}

result = subprocess.run(
    ["ntn", "api", "v1/pages", "-d", json.dumps(payload)],
    capture_output=True, text=True
)
d = json.loads(result.stdout)
notion_page_id = d["id"].replace("-", "")
```

**성공**: `id` 포함 응답 → `notion_page_id` 기록
**실패**: 에러 출력 후 경고 — `discord_message_id`는 저장, `notion_page_id` 비워둠

> **Drops DB 실제 속성 타입** (ntn으로 확인한 기준):
> `Drop` (title) / `Type` (select) / `Source` (rich_text) / `Link` (url) /
> `Memo` (rich_text) / `Visibility` (select) / `Date` (date) /
> `Attachments` (files) / `Author` (people) / `Tags` (multi_select)

---

## Step 5: CSV 갱신

`04_studio/drops/drops.csv`에 새 행 추가:

```python
# Python으로 CSV append (bash heredoc 사용)
python3 -c "
import csv, sys
row = [
    '{drop_number}',
    '{title}',
    '{type}',
    '{tags — drop/* 접두사 제거, /로 구분}',
    '{source}',
    '{link}',
    '{memo}',
    '{visibility}',
    '{created}',
    '{published_at — 오늘 날짜}',
    '{discord_message_id}',
    '{notion_page_id}'
]
with open('04_studio/drops/drops.csv', 'a', newline='', encoding='utf-8') as f:
    writer = csv.writer(f)
    writer.writerow(row)
print('CSV 갱신 완료')
"
```

---

## Step 6: Frontmatter 갱신

Edit 도구로 현재 파일의 frontmatter 업데이트:

```yaml
status: live
drop_status: live
published_at: {오늘 날짜 YYYY-MM-DD}
discord_message_id: "{discord_message_id}"
notion_page_id: "{notion_page_id}"
```

---

## Step 7: 칸반 보드 동기화

frontmatter가 `drop_status: live`로 갱신되었으므로 칸반 보드를 동기화한다:

```bash
_system/tools/kanban/kanban-sync.sh
```

발행된 드롭이 `✅ 발행 완료` 컬럼에 없으면 자동으로 추가되며, 이미 있는 경우는 건너뛴다.

---

## Step 8: 완료 보고

사용자에게 출력:

```
✅ Drop #{drop_number} 발행 완료

Discord: https://discord.com/channels/$DISCORD_GUILD_ID/[Discord-채널-ID]/{discord_message_id}
Notion: https://www.notion.so/{notion_page_id}
CSV: 04_studio/drops/drops.csv 갱신
칸반: _system/obsidian/kanban/칸반-드롭.md 동기화 완료

파일: {현재 파일명}
```

---

## 오류 처리 요약

| 단계 | 실패 시 |
|------|---------|
| Discord | 즉시 중단. frontmatter 미변경. 오류 메시지 출력. |
| Notion | 경고 출력. discord_message_id는 저장. notion_page_id 비워둠. |
| CSV | 경고 출력. 수동 추가 필요 안내. |
| Frontmatter | Edit 실패 시 수동 수정 안내. |
