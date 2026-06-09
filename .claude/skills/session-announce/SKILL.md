---
name: session-announce
description: 세션 공지 자동화 — 포스터 PNG 캡처 + Discord #announcements 초안을 발행 직전까지 준비한다
argument-hint: "[세션ID: S01|S02|...] [선택: announce|ops]"
allowed-tools: Read, Edit, Write, Bash, mcp__plugin_chrome-devtools-mcp_chrome-devtools__new_page, mcp__plugin_chrome-devtools-mcp_chrome-devtools__navigate_page, mcp__plugin_chrome-devtools-mcp_chrome-devtools__evaluate_script, mcp__plugin_chrome-devtools-mcp_chrome-devtools__take_screenshot
---

# 세션 공지 자동화

## 목적

세션 기획 확정 후, 포스터 PNG 캡처부터 Discord 공지 초안까지 **발행 직전 상태**로 준비한다.  
사람이 할 일: Discord에 붙여넣기 + 이미지 첨부 → 완료.

---

## 입력

| 인자 | 필수 | 설명 |
|------|------|------|
| `세션ID` | 선택 | `S01`, `S02` 등. 없으면 active-context에서 가장 가까운 세션 파악 |
| `채널유형` | 선택 | `announce` (기본, `#announcements`) / `ops` (`#ops-general`) |

---

## 전제 조건

- 세션 plan 파일이 존재하고 날짜·장소·프로그램이 확정되어 있어야 함
- `04_studio/brand/web/260525-poster-template-v1.html` — 포스터 템플릿 존재
- Chrome DevTools MCP 연결됨

---

## 절차

### 1. 컨텍스트 파악

```
_system/agents/memory/active-context.md       ← 현재 상태·다음 세션
02_sessions/{세션ID}-*/S*-*-plan.md           ← 날짜·장소·프로그램·발표자
```

plan 파일에서 추출할 정보:

| 필드 | 예시 |
|------|------|
| `label` | `SESSION 01` |
| `title` | `["AI 시대,", "AX 시작"]` |
| `subtitle` | `"2026 Spring · 첫 정규세션"` |
| `tags` | `["AX", "컨텍스트", "오프라인"]` |
| `program` | `[{part, title, presenter}, ...]` |
| `date` | `"05.30 (토)"` |
| `time` | `"10:00 ~ 12:00"` |
| `place` | `"충남대 도서관 크리에이티브존1\n별관1층 그룹스터디룸 9"` |

### 2. 포스터 템플릿 업데이트

`04_studio/brand/web/260525-poster-template-v1.html` 최상단 `POSTER` 객체를 세션 정보로 수정:

```js
const POSTER = {
  label:    "SESSION 01",
  theme:    "silver",          // 세션은 silver 기본, 특별 이벤트는 amber
  title:    ["AI 시대,", "AX 시작"],
  subtitle: "2026 Spring  ·  첫 정규세션",
  tags:     ["AX", "컨텍스트", "오프라인"],
  program: [
    { part: "1부", title: "AI 시대, 왜 지금인가", presenter: "주용" },
    { part: "2부", title: "대학생활에서 AX 시작하기", presenter: "동하" },
  ],
  date:   "05.30 (토)",
  time:   "10:00 ~ 12:00",
  place:  "충남대 도서관 크리에이티브존1\n별관1층 그룹스터디룸 9",
  visual: "orbit",
};
```

**theme 참조표**

| theme | 색상 | 용도 |
|-------|------|------|
| `silver` | #D0D0D0 | 정규 세션 기본 |
| `purple` | #7C3AED | 시즌 오픈 |
| `blue` | #B4DCFF | 딥다이브·리서치 |
| `teal` | #14B8A6 | 커뮤니티·네트워킹 |
| `amber` | #F59E0B | 스페셜 이벤트 |

### 3. 포스터 PNG 캡처

#### 3-1. Chrome DevTools MCP로 열기

```
new_page url="file:///절대경로/04_studio/brand/web/260525-poster-template-v1.html"
```

이미 열려 있으면:
```
navigate_page type="reload"
```

#### 3-2. 포스터 위치 파악

```js
// evaluate_script
() => {
  const el = document.querySelector('.poster');
  const r = el.getBoundingClientRect();
  return { x: r.x, y: r.y, w: r.width, h: r.height, dpr: window.devicePixelRatio };
}
// 반환 예시: { x: 48, y: 48, w: 488, h: 610, dpr: 1 }
```

#### 3-3. 전체 페이지 스크린샷 → sips 크롭

```bash
# 1) Chrome DevTools take_screenshot (fullPage: true)
#    filePath: "04_studio/assets/etc/YYMMDD-{세션ID}-poster-full.png"

# 2) sips 크롭 (DPR=1 기준 x=48, y=48, 488×610)
sips -c 610 488 --cropOffset 48 48 \
  "04_studio/assets/etc/YYMMDD-{세션ID}-poster-full.png" \
  --out "04_studio/assets/etc/YYMMDD-{세션ID}-poster.png"

# 3) 크기 검증
sips -g pixelWidth -g pixelHeight "04_studio/assets/etc/YYMMDD-{세션ID}-poster.png"
# 예상: pixelWidth: 488, pixelHeight: 610

# 4) 임시 파일 제거
rm "04_studio/assets/etc/YYMMDD-{세션ID}-poster-full.png"
```

> **DPR 주의**: `dpr > 1`이면 x·y·w·h 값에 dpr을 곱해서 sips 인자를 계산한다.  
> 예: dpr=2 → `--cropOffset 96 96`, `-c 1220 976`

#### 3-4. 이미지 시각 확인

`Read` 도구로 PNG를 읽어 레이아웃이 정상인지 확인. 오류 시 템플릿 수정 후 3-1부터 재실행.

### 4. Discord 공지 초안 작성

파일명: `01_ops/posts/YYMMDD-announce-{세션슬러그}세션공지.md`

**frontmatter**:
```yaml
---
title: {세션명} 세션 공지
kind: post
area: ops
status: draft
created: YYYY-MM-DD
target_channel: "#announcements"
tags:
  - ops/post
  - session/{세션ID}
---
```

**발행문 형식** (`#announcements`, 200–350자):

```
📢 **{세션ID} 세션 — {세션 제목}**

{훅 문장 1–2줄 — 세션 핵심 메시지 기반}

📅 **{날짜} {시간}**
📍 {장소}

{프로그램 목록 (1부/2부/번외)}

오시는 분은 `#general`에 한 줄 남겨주세요 :)
```

**`#calendar` 등록 텍스트** (별도 섹션):
```
📅 {세션ID} 세션 | {날짜} {시간}
{장소}
```

**발행 체크리스트**:
```markdown
- [ ] 포스터 PNG: `04_studio/assets/etc/YYMMDD-{세션ID}-poster.png`
- [ ] `#announcements` — 발행문 붙여넣기 + 포스터 첨부
- [ ] `#calendar` — 일정 한 줄 등록
- [ ] 발행 후 이 파일 `status: live`로 변경
```

### 5. #sessions 채널 발행

> `#announcements` 초안 저장 후, `#sessions` 채널에는 **코드블록(`` ` `` `` ` `` `` ` ``)으로 감싼 포맷**으로 직접 발행한다.  
> 이유: 여러 칸 띄어쓰기 정렬이 monospace(코드블록)에서만 보존된다. 이모지는 코드블록 안에서도 정상 렌더된다.

#### 5-1. 발행 포맷

Discord에 게시할 실제 메시지는 아래 형태로 ` ``` ` 로 전체를 감싼다:

````
```
📢 S{NN} 세션 — {제목}

{1~2줄 후킹 소개}

📅 {날짜} · {시간}
📍 {장소}

{진행자}
{모듈/파트 한 줄 요약}
```
````

#### 5-2. 채널 정보

| 채널 | ID |
|------|-----|
| `#sessions` | `[Discord-채널-ID]` |

Guild ID: `[Discord-채널-ID]`

#### 5-3. 발행

> 함정 패턴 (User-Agent 필수, UTF-8, message_id): `_system/platform/Discord/260608-Discord-API-레시피.md` 참조.

```bash
source "_system/tools/.env"
export PYTHONUTF8=1

python3 - <<'PYEOF'
import json, pathlib

content = """\
\`\`\`
📢 S{NN} 세션 — {제목}

{후킹 소개}

📅 {날짜} · {시간}
📍 {장소}

{진행자} / {파트 요약}
\`\`\`"""

pathlib.Path("/tmp/discord_payload.json").write_text(
    json.dumps({"content": content}, ensure_ascii=False), encoding="utf-8"
)
PYEOF

curl -s -X POST "https://discord.com/api/v10/channels/[Discord-채널-ID]/messages" \
  -H "Authorization: Bot $DISCORD_BOT_TOKEN" \
  -H "User-Agent: DiscordBot (https://[클럽명].club, 1.0)" \
  -H "Content-Type: application/json" \
  --data @/tmp/discord_payload.json

rm /tmp/discord_payload.json
```

---

### 6. 완료 보고

- 초안 파일 경로
- 발행문 전체 (대화창 출력 — 바로 복사 가능)
- 포스터 PNG 경로

---

## 파일 경로 규칙

| 파일 | 경로 |
|------|------|
| 포스터 템플릿 | `04_studio/brand/web/260525-poster-template-v1.html` |
| 포스터 PNG | `04_studio/assets/etc/YYMMDD-{세션ID}-poster.png` |
| 공지 초안 | `01_ops/posts/YYMMDD-announce-{세션슬러그}세션공지.md` |

---

## 전체 파이프라인

```
세션 plan 확정
  ↓
poster-template-v1.html POSTER 객체 업데이트 (Edit)
  ↓
Chrome DevTools MCP → new_page → evaluate_script (위치 파악)
  → take_screenshot fullPage → Bash sips 크롭 → PNG 저장
  ↓
Discord 초안 작성 → 01_ops/posts/ 저장
  ↓
#sessions 채널 발행 — python payload + curl (레시피: 260608-Discord-API-레시피.md)
  ↓
[사람] #announcements 붙여넣기 + 포스터 첨부 → 발행 완료
       #calendar 일정 등록
```

---

## 관련 스킬

- `discord-announce` — 포스터 없는 일반 Discord 공지
- `drop-announce` — Drop 공지 자동화 (Excalidraw 카드 포함)
- `slide-prep` — 슬라이드 제작 준비
