---
name: release
description: 기능 기반 릴리즈 — AI 초안 제안 → 승인 → git push + GitHub release + Discord 공지 일괄 실행
argument-hint: "[버전태그 — 생략 시 자동 추론]"
allowed-tools: Read, Bash, Edit
---

# /release — 릴리즈 스킬

## 목적

변경사항을 스캔하여 버전 태그·GitHub 릴리즈 노트·Discord 공지 초안을 AI가 작성하고,
사람의 1회 승인 후 git push → GitHub release 생성 → Discord 공지 발행을 일괄 실행한다.

---

## 전체 흐름

```
/release [버전] 호출
    ↓
Step 1: 스캔 — change-log.md 최신 섹션 + git log (마지막 태그 이후)
    ↓
Step 2: AI 초안 생성 — 버전 태그 / GitHub 릴리즈 노트 / Discord 공지 텍스트
    ↓
Step 3: 전체 초안 제시 → 사용자 수정 가능 → "go" 승인 대기
    ↓
Step 4: 일괄 실행 — git add -A → commit → push → gh release create → Discord API
    ↓
Step 5: 완료 — ops 공지 파일 저장 + change-log 갱신
```

---

## Step 1: 스캔

### 1-1. change-log.md 최신 섹션 읽기

```bash
head -80 _system/agents/memory/change-log.md
```

`## YYYY-MM-DD` 헤더 기준으로 마지막 태그 이후 섹션들을 파악한다.

### 1-2. git log 읽기 (마지막 태그 이후 커밋)

```bash
LAST_TAG=$(git describe --tags --abbrev=0 2>/dev/null)
if [ -n "$LAST_TAG" ]; then
  echo "마지막 태그: $LAST_TAG"
  git log ${LAST_TAG}..HEAD --oneline
else
  echo "태그 없음 — 전체 최근 20개"
  git log --oneline -20
fi
```

### 1-3. 미커밋 변경사항 확인

```bash
git status --short
```

---

## Step 2: AI 초안 생성

스캔 결과를 바탕으로 다음 3가지를 직접 작성한다. 도구 호출 없이 텍스트로만 출력.

### 버전 태그 추론 규칙

인자로 버전이 제공된 경우 그대로 사용. 생략된 경우 아래 규칙으로 추론:

| 변경 유형 | 버전 증가 방식 | 예시 |
|-----------|--------------|------|
| 새 시스템·스킬·에이전트 추가 | `v0.X` 마이너 증가 | v0.3 → v0.4 |
| 기존 시스템 개선·버그 수정·문서 갱신 | `v0.X.Y` 패치 증가 | v0.4 → v0.4.1 |
| 시즌 오픈·대형 구조 변경·메이저 마일스톤 | 메이저 증가 | v0.X → v1.0 |

추론 근거를 반드시 한 줄로 설명한다.

### GitHub 릴리즈 노트 초안 (한국어)

```
## {버전} — {날짜}

### 추가
- {새 기능/스킬/구조}

### 개선
- {기존 기능 수정·향상}

### 수정
- {버그 픽스·오타·누락}
```

변경이 없는 섹션은 생략한다.

### Discord 공지 초안 (ops 스레드용)

```
**🚀 {버전} 릴리즈** | {날짜}

{1-2문장 변경사항 요약}

**주요 변경**
① {항목 1}
② {항목 2}
(항목 반복)

---
반영 사항 있으면 알려주세요.
```

길이 기준: 400–700자. 이모지는 헤더에만 1개.

---

## Step 3: 승인 게이트

초안 전체를 아래 형식으로 사용자에게 제시한다:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔖 버전 태그:  {버전}
   (추론 근거: {근거 한 줄})

📋 GitHub 릴리즈 노트:
{릴리즈 노트 전문}

💬 Discord 공지 (ops 스레드):
{Discord 공지 전문}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

수정이 필요하면 지금 알려주세요.
확인되었으면 "go"를 입력하세요.
```

**"go" 입력 전까지 git, gh, curl 등 실행 명령어를 절대 호출하지 않는다.**

사용자가 수정 내용을 전달하면 해당 부분만 수정 후 다시 제시한다.
버전 태그를 직접 지정하면 그 값을 사용한다.

---

## Step 4: 일괄 실행

"go" 승인 후 순서대로 실행. 각 단계 실패 시 즉시 중단하고 오류를 보고한다.

### 4-1. 환경변수 로드 및 확인

```bash
source _system/tools/.env
echo "봇 토큰 확인: ${DISCORD_BOT_TOKEN:0:10}..."
echo "ops 스레드 ID: $DISCORD_OPS_ANNOUNCEMENT_THREAD_ID"
```

`DISCORD_BOT_TOKEN` 또는 `DISCORD_OPS_ANNOUNCEMENT_THREAD_ID`가 비어 있으면 중단.

### 4-2. git add → commit → push

```bash
git add -A
git commit -m "release: {버전}

{릴리즈 노트 요약 2-3줄}"
git push
```

커밋 메시지 타입은 반드시 `release:`. push 실패 시 즉시 중단.

### 4-3. GitHub 릴리즈 생성

```bash
source _system/tools/.env

gh release create {버전} \
  --title "{버전} — {날짜}" \
  --notes "{Step 2에서 작성한 GitHub 릴리즈 노트 전문}" \
  -R [사용자명]/[워크스페이스-레포]
```

성공하면 릴리즈 URL을 출력. 실패 시 즉시 중단.

### 4-4. Discord 공지 발행 (ops 스레드)

> 함정 패턴 (User-Agent 필수, UTF-8, message_id): `_system/platform/Discord/260608-Discord-API-레시피.md` 참조.

```bash
source _system/tools/.env
export PYTHONUTF8=1

python3 - <<'PYEOF'
import json, pathlib

content = """{Step 2에서 작성한 Discord 공지 전문}"""

pathlib.Path("/tmp/discord_release.json").write_text(
    json.dumps({"content": content}, ensure_ascii=False), encoding="utf-8"
)
PYEOF

DISCORD_MSG_ID=$(curl -s -X POST \
  "https://discord.com/api/v10/channels/$DISCORD_OPS_ANNOUNCEMENT_THREAD_ID/messages" \
  -H "Authorization: Bot $DISCORD_BOT_TOKEN" \
  -H "User-Agent: DiscordBot (https://[클럽명].club, 1.0)" \
  -H "Content-Type: application/json" \
  --data @/tmp/discord_release.json \
  | python3 -c "import json,sys; d=json.load(sys.stdin); print(d.get('id','ERROR'))")

rm /tmp/discord_release.json
echo "Discord message ID: $DISCORD_MSG_ID"
```

`id` 필드가 있으면 성공. `ERROR`가 출력되면 Discord API 오류.
Discord 실패 시: git push와 GitHub release는 이미 완료이므로 중단하지 않고 경고 출력 + 수동 발행 안내.

#### 4-4-이미지. 릴리즈에 이미지(스크린샷·그래프 등)가 포함되는 경우

이미지를 Discord에 첨부할 때는 `-H "Content-Type: application/json"` 헤더를 **생략**하고 multipart 폼으로 전송한다.
`Content-Type` 헤더를 명시하면 curl의 자동 `multipart/form-data` 설정을 덮어써 업로드가 실패한다.

```bash
source _system/tools/.env

DISCORD_MSG_ID=$(curl -s -X POST \
  "https://discord.com/api/v10/channels/$DISCORD_OPS_ANNOUNCEMENT_THREAD_ID/messages" \
  -H "Authorization: Bot $DISCORD_BOT_TOKEN" \
  -H "User-Agent: DiscordBot (https://[클럽명].club, 1.0)" \
  -F 'payload_json={"content":"**🚀 v0.X 릴리즈** | 2026-MM-DD\n\n공지 텍스트 전문..."};type=application/json' \
  -F "files[0]=@/path/to/screenshot.png" \
  | python3 -c "import json,sys; d=json.load(sys.stdin); print(d.get('id','ERROR'))")

echo "Discord message ID: $DISCORD_MSG_ID"
```

복수 파일: `-F "files[1]=@/path/to/second.png"` 등 인덱스 증가. `payload_json` 값에 `\n` 사용 가능.

---

## Step 5: 완료 처리

### 5-1. change-log.md 갱신 (append)

`_system/agents/memory/change-log.md`의 기존 첫 번째 `##` 섹션 바로 위에 삽입:

```markdown
## {오늘 날짜} — 릴리즈 {버전}

- GitHub Release: {버전}
- 릴리즈 URL: {gh release URL}
- Discord 공지: ops 스레드 (message ID: {discord_msg_id})
- 주요 변경: {릴리즈 노트 요약 1-2줄}
```

Edit 도구 사용. 첫 번째 `## ` 앞에 위 섹션을 삽입한다.

### 5-2. 루트 CHANGELOG.md 갱신 (append)

루트 `CHANGELOG.md`의 첫 번째 `## v` 항목 바로 위에 아래 섹션을 삽입한다.
포맷은 기존 항목과 동일하게: `## {버전} — {YYYY-MM-DD}` 헤더 + `### 추가/개선/수정` 섹션(변경 없는 섹션 생략).

```markdown
## {버전} — {날짜}

### 추가
- {새 기능·스킬·구조}

### 개선
- {기존 기능 수정·향상}
```

Edit 도구 사용. `change-log.md`의 운영 변경사항을 이 파일에서는 기능 단위로 압축해 기록한다.

> **이 스텝이 누락되면 루트 CHANGELOG에 버전이 쌓이지 않는다 — v0.16·v0.17이 누락된 원인.**

### 5-3. ops 공지 파일 저장

발행한 Discord 공지를 `01_ops/posts/{YYMMDD}-릴리즈-{버전}.md`에 저장한다.
파일명: 6자리 날짜 + 버전 (예: `260609-릴리즈-v0.17.md`)

**frontmatter (필수 필드)**:

```yaml
title: 릴리즈 {버전} 공지 (ops)
kind: post
area: ops
status: live
published_at: {오늘 날짜 YYYY-MM-DD}
discord_message_id: "{discord_msg_id}"
created: {오늘 날짜 YYYY-MM-DD}
target_channel: "ops 공지 스레드"
up: "[[MOC-운영]]"
tags:
  - ops/post
  - ops/release
```

Discord 발행에 실패한 경우: `discord_message_id` 필드를 생략하고 발행 체크리스트를 `[ ]`로 남긴다.

**본문 구조**:

1. 헤더 블록 (`>` 인용) — 대상 채널 · 이전 버전 · GitHub URL
2. `## Discord 발행문` — Step 2 Discord 공지 전문을 코드블록(` ``` `)으로 감싸 삽입
3. `## 첨부 이미지` — **이미지가 있는 릴리즈만** 포함:
   ```
   ![[이미지명.확장자]]
   ```
   이미지 없는 릴리즈는 이 섹션을 생략한다.
4. `## 발행 체크리스트` — 항목별 완료 여부 + 날짜

**체크리스트 항목**:

```markdown
- [x] 발행문 ops 공지 스레드에 발행 ✅ {날짜} (msg ID `{discord_msg_id}`)
- [x] GitHub 릴리즈 노트 생성 ✅ {버전}
- [x] message ID `_system/agents/memory/change-log.md` {날짜} {버전} 섹션에 기록 ✅
```

### 5-4. 완료 보고

```
✅ 릴리즈 완료: {버전}

git:    커밋 + push 완료
GitHub: {릴리즈 URL}
Discord: ops 스레드 발행 완료 (ID: {discord_msg_id})

change-log.md 갱신 완료
CHANGELOG.md(루트) 갱신 완료
ops 공지 파일 저장: 01_ops/posts/{YYMMDD}-릴리즈-{버전}.md
```

---

## 오류 처리 요약

| 단계 | 실패 시 동작 |
|------|------------|
| 환경변수 로드 | 즉시 중단. `.env` 확인 안내. |
| git push | 즉시 중단. 이후 단계 실행 안 함. |
| gh release create | 즉시 중단. Discord 발행 안 함. |
| Discord API | 경고 출력 + 수동 발행 안내. 릴리즈는 완료로 처리. |
| change-log 갱신 | 경고 출력 + 수동 추가 안내. |
| CHANGELOG.md(루트) 갱신 | 경고 출력 + 수동 추가 안내. |
| ops 공지 파일 저장 | 경고 출력 + 수동 저장 안내. 릴리즈는 완료로 처리. |

---

## 주의사항

- Step 3 "go" 승인 전까지 git, gh, curl 명령어를 절대 실행하지 않는다.
- 버전 태그는 항상 `v` 접두사 포함 (예: `v0.4`, `v0.4.1`, `v1.0`).
- `_system/tools/.env` 내용(토큰 값)을 출력하거나 로그에 남기지 않는다.
- GitHub 저장소: `[사용자명]/[워크스페이스-레포]`
- Discord ops 스레드 채널 ID: `$DISCORD_OPS_ANNOUNCEMENT_THREAD_ID` (`.env`에서 로드)
