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

### author → Discord 멘션 변환

`_system/tools/.env`의 `DISCORD_USER_{이름}` 변수에서 매핑을 자동으로 구성한다:

```python
import os
# 환경변수 DISCORD_USER_{이름} 전체를 읽어 매핑 구성
author_ids = {}
for k, v in os.environ.items():
    if k.startswith('DISCORD_USER_'):
        name = k[len('DISCORD_USER_'):]
        author_ids[name] = v

author = "{frontmatter에서 읽은 author 값}"
mention = f"<@{author_ids[author]}>" if author in author_ids else ""
```

**운영자 ID 등록**: `/platform-setup`을 실행하면 이름↔ID 매핑을 `.env`에 추가할 수 있다.

### 메시지 포맷 구성

```
**{title에서 "Drop #NNNN — " 제거한 제목}**

{## 공유 내용 본문}

{## 링크 내용}

[ 공유 | #{drop_number} ]{" — " + mention if mention else ""}
```

예: `[ 공유 | #0017 ] — <@{ID1}>`

### API 호출

```bash
source _system/tools/.env

MESSAGE_JSON=$(python3 -c "
import json, sys
content = '''여기에 구성된 메시지 내용'''
print(json.dumps({'content': content}))
")

RESPONSE=$(curl -s -X POST \
  "https://discord.com/api/v10/channels/$DISCORD_SHARE_CHANNEL_ID/messages" \
  -H "Authorization: Bot $DISCORD_BOT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "$MESSAGE_JSON")

echo "$RESPONSE"
MESSAGE_ID=$(echo "$RESPONSE" | python3 -c "import json,sys; print(json.load(sys.stdin)['id'])")
echo "Discord message ID: $MESSAGE_ID"
```

**성공**: `id` 필드가 있는 JSON 응답
**실패**: `code` 필드가 있는 에러 → 에러 내용 출력 후 **중단** (Notion 진행 안 함)

---

## Step 4: Notion 발행

`notion_page_id`가 이미 있으면 이 단계를 **skip**.

### Notion MCP 호출

`notion-create-pages` 도구 사용:

```
parent_id: {NOTION_DROPS_DB_ID — 환경변수에서 로드: $NOTION_DROPS_DB_ID}
properties:
  Drop (title): "{title}"
  Type (select): "{type}"
  Tags (multi_select): [drop/* 접두사 제거한 태그들]
    예: "drop/AI" → "AI", "drop/PKM" → "PKM"
  Source (text): "{source}"
  Link (url): "{link}"
  Memo (text): "{memo}"
  Visibility (select): "{visibility}"
  Date (date): "{published_at 또는 오늘 날짜}"

page body (children):
  paragraph: "## 공유 내용\n\n{본문 텍스트}"
```

**성공**: page_id 포함 응답
**실패**: 에러 출력 후 경고 — discord_message_id는 저장, notion_page_id는 비워둠

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

## Step 7: 완료 보고

사용자에게 출력:

```
✅ Drop #{drop_number} 발행 완료

Discord: https://discord.com/channels/$DISCORD_GUILD_ID/1486411209556361306/{discord_message_id}
Notion: https://www.notion.so/{notion_page_id}
CSV: 04_studio/drops/drops.csv 갱신

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
