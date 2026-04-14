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
Step 5: 완료 — change-log에 릴리즈 기록
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
  -R River-181/[조직명]-workspace
```

성공하면 릴리즈 URL을 출력. 실패 시 즉시 중단.

### 4-4. Discord 공지 발행 (ops 스레드)

```bash
source _system/tools/.env

DISCORD_MSG=$(python3 -c "
import json
content = '''{Step 2에서 작성한 Discord 공지 전문}'''
print(json.dumps({'content': content}))
")

RESPONSE=$(curl -s -X POST \
  "https://discord.com/api/v10/channels/$DISCORD_OPS_ANNOUNCEMENT_THREAD_ID/messages" \
  -H "Authorization: Bot $DISCORD_BOT_TOKEN" \
  -H "User-Agent: DiscordBot (https://[클럽URL], 1.0)" \
  -H "Content-Type: application/json" \
  -d "$DISCORD_MSG")

echo "$RESPONSE"
DISCORD_MSG_ID=$(echo "$RESPONSE" | python3 -c "import json,sys; d=json.load(sys.stdin); print(d.get('id','ERROR'))")
echo "Discord message ID: $DISCORD_MSG_ID"
```

`id` 필드가 있으면 성공. `code` 필드가 있으면 Discord API 오류.
Discord 실패 시: git push와 GitHub release는 이미 완료이므로 중단하지 않고 경고 출력 + 수동 발행 안내.

**Discord 공지 텍스트에 작은따옴표(')가 포함된 경우 python3 heredoc 충돌 방지:**

```bash
# 공지 텍스트를 파일로 저장 후 읽기
cat > /tmp/discord_msg.json << 'JSONEOF'
{"content": "{공지 내용 — 작은따옴표 있을 경우 이 방식 사용}"}
JSONEOF

RESPONSE=$(curl -s -X POST \
  "https://discord.com/api/v10/channels/$DISCORD_OPS_ANNOUNCEMENT_THREAD_ID/messages" \
  -H "Authorization: Bot $DISCORD_BOT_TOKEN" \
  -H "User-Agent: DiscordBot (https://[클럽URL], 1.0)" \
  -H "Content-Type: application/json" \
  -d @/tmp/discord_msg.json)
```

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

### 5-2. 완료 보고

```
✅ 릴리즈 완료: {버전}

git:    커밋 + push 완료
GitHub: {릴리즈 URL}
Discord: ops 스레드 발행 완료 (ID: {discord_msg_id})

change-log.md 갱신 완료
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

---

## 주의사항

- Step 3 "go" 승인 전까지 git, gh, curl 명령어를 절대 실행하지 않는다.
- 버전 태그는 항상 `v` 접두사 포함 (예: `v0.4`, `v0.4.1`, `v1.0`).
- `_system/tools/.env` 내용(토큰 값)을 출력하거나 로그에 남기지 않는다.
- GitHub 저장소: `River-181/[조직명]-workspace`
- Discord ops 스레드 채널 ID: `$DISCORD_OPS_ANNOUNCEMENT_THREAD_ID` (`.env`에서 로드)
