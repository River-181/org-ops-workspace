---
name: drop-announce
description: Drop 공지 자동화 — 유동적 항목 개수 지원, 이미지 스마트 캡처, Drop 파일 생성, /drop-publish 자동 호출
argument-hint: "<번호> \"<제목>\" \"<타입>\" (항목은 stdin에서 N개 읽기)"
---

# /drop-announce — Drop 공지 자동화 스킬

## 목적

Drop 공지 발행을 **한 번에 자동화**:
1. **Excalidraw 카드 다이어그램** 동적 생성 (항목 N개, 1~4개)
2. **웹사이트/오브젝트 스마트 캡처** — 해당 요소만 정밀 캡처
3. **Drop 파일** 생성 (정확한 속성 순서 + 콘텐츠 + 이미지 임베드)
4. **MAP-드롭 그래프 링크** 자동 추가 (`_system/obsidian/maps/MAP-드롭.md`)
5. **자동으로** `/drop-publish` 호출 → Discord #share + Notion 동시 발행
6. **칸반 자동 업데이트** (선택사항)

---

## 입력 형식

```bash
/drop-announce <번호> "<제목>" "<타입>"
```

그 후 항목을 자유롭게 입력 (1개 ~ N개):

```
① <항목명> | <링크> | by <제공자> | <한줄설명>
② <항목명> | <링크> | by <제공자> | <한줄설명>
(필요한 만큼 반복)
```

### 예시 1: 4개 항목

```bash
/drop-announce 0017 "이번 주 도구 4가지" "Tools"
① Korean Law MCP | https://github.com/chrisryugj/korean-law-mcp | by Chris (@chrisryugj) | 한국 법률 DB 연동
② Meta AI — 뇌 반응 예측 | https://www.meta.com/research/ | by Meta Research | UI/UX 리서치용 AI
③ Anthropic Science Hub | https://www.anthropic.com/science | by Anthropic | Claude 개발 뒤의 과학
④ NotebookLM MCP | https://notebooklm.google.com/ | by Google | 리서치 노트 AI 자동화
```

### 예시 2: 1개 항목 (단순 공유)

```bash
/drop-announce 0018 "새로운 Claude 모델 출시" "News"
① Claude 4.5 발표 | https://www.anthropic.com/news/claude-4.5 | by Anthropic | 최신 AI 모델 공개
```

### 예시 3: 2개 항목 (비교 분석)

```bash
/drop-announce 0019 "AI 모델 비교" "Research"
① GPT-4 vs Claude | https://comparison.com/gpt-vs-claude | by OpenAI/Anthropic | 성능 비교 분석
② 코드 생성 능력 벤치마크 | https://benchmark.com/llm | by HuggingFace | 정량 데이터
```

---

## 절차

### Step 0: 입력 파싱 (유동적)

```bash
DROP_NUMBER=$(printf "%04d" $1)  # "0017"
DROP_TITLE="$2"                  # "이번 주 도구 4가지"
DROP_TYPE="$3"                   # "Tools" (기본값: "News")
TODAY=$(date +%Y-%m-%d)
FILENAME=$(date +%y%m%d)

# 항목 개수 동적 읽기
ITEMS=()
COUNT=0
while IFS='|' read -r NUM NAME LINK AUTHOR DESC; do
    NUM=$(echo "$NUM" | xargs)      # ① 제거
    NAME=$(echo "$NAME" | xargs)
    LINK=$(echo "$LINK" | xargs)
    AUTHOR=$(echo "$AUTHOR" | xargs)
    DESC=$(echo "$DESC" | xargs)
    
    ITEMS+=("$NUM|$NAME|$LINK|$AUTHOR|$DESC")
    ((COUNT++))
done

echo "📋 항목 개수: $COUNT개"
```

### Step 1: 폴더 생성

```bash
ASSETS_DIR="04_studio/assets/etc"
mkdir -p "$ASSETS_DIR"
```

### Step 2: Excalidraw 카드 다이어그램 (동적 생성)

```bash
# 항목 개수에 따라 격자 동적 계산
# 1~2개: 1열, 3~4개: 2열
COLS=$((COUNT > 2 ? 2 : 1))
ROWS=$((($COUNT + $COLS - 1) / $COLS))

# 카드 크기 및 간격
CARD_WIDTH=320
CARD_HEIGHT=200
CARD_X_OFFSET=40
CARD_Y_OFFSET=80
GAP_X=380
GAP_Y=240

# JSON 카드 생성 (각 항목별)
ELEMENTS="["
for i in "${!ITEMS[@]}"; do
    IFS='|' read -r NUM NAME LINK AUTHOR DESC <<< "${ITEMS[$i]}"
    
    COL=$((i % COLS))
    ROW=$((i / COLS))
    X=$((CARD_X_OFFSET + COL * GAP_X))
    Y=$((CARD_Y_OFFSET + ROW * GAP_Y))
    
    # 색상 로테이션
    COLORS=("#d3f9d8" "#ffd8a8" "#a5d8ff" "#d0bfff")
    COLOR=${COLORS[$i % 4]}
    
    ELEMENTS+="
    {
      \"type\": \"rectangle\",
      \"id\": \"card$((i+1))\",
      \"x\": $X,
      \"y\": $Y,
      \"width\": $CARD_WIDTH,
      \"height\": $CARD_HEIGHT,
      \"backgroundColor\": \"$COLOR\",
      \"fillStyle\": \"solid\",
      \"roundness\": {\"type\": 3},
      \"strokeColor\": \"#1e1e1e\",
      \"label\": {
        \"text\": \"$NUM $NAME\\nby $AUTHOR\\n\\n$DESC\",
        \"fontSize\": 16
      }
    },"
done
ELEMENTS="${ELEMENTS%,}]"

# Excalidraw JSON 완성
cat > /tmp/excalidraw.json << EOF
{
  "type": "excalidraw",
  "elements": $ELEMENTS,
  "appState": {"zoom": 1}
}
EOF

# Excalidraw 온라인 링크로 변환
EXCALIDRAW_URL="https://excalidraw.com/#json=$(cat /tmp/excalidraw.json | base64 -w0)"
echo "1️⃣ Excalidraw 다이어그램: $EXCALIDRAW_URL"
```

### Step 3: 스마트 스크린샷 캡처 (정밀)

```bash
# 환경변수 로드
source _system/tools/.env

# 각 링크에서 오브젝트별 스크린샷 캡처
capture_smart_screenshot() {
    local url=$1
    local output=$2
    local description=$3
    
    echo "📸 캡처 중: $description"
    
    # Safari 열기 + 로딩 대기
    open -a Safari "$url"
    sleep 4
    
    # 웹사이트 유형별 정밀 캡처 (오브젝트 중심)
    if [[ "$url" == *"github.com"* ]]; then
        # GitHub: 리포지토리 헤더 영역만 캡처
        screencapture -t png -R 0,100,1920,400 "$ASSETS_DIR/$output.png"
    elif [[ "$url" == *"meta.com"* ]]; then
        # Meta: 콘텐츠 본체 영역 캡처
        screencapture -t png -R 0,200,1920,600 "$ASSETS_DIR/$output.png"
    elif [[ "$url" == *"anthropic.com"* ]]; then
        # Anthropic: 메인 섹션 캡처
        screencapture -t png -R 0,150,1920,550 "$ASSETS_DIR/$output.png"
    elif [[ "$url" == *"google.com"* ]] || [[ "$url" == *"notebooklm"* ]]; then
        # Google: UI 인터페이스 중심 캡처
        screencapture -t png -R 0,100,1920,500 "$ASSETS_DIR/$output.png"
    elif [[ "$url" == *"excalidraw"* ]]; then
        # Excalidraw: 캔버스 전체 캡처 (다이어그램)
        screencapture -t png -R 100,100,1720,820 "$ASSETS_DIR/$output.png"
    else
        # 기본: 뷰포트 중심 영역 캡처
        screencapture -t png -R 0,100,1920,700 "$ASSETS_DIR/$output.png"
    fi
    
    echo "✅ 저장: $output.png"
}

# 1. Excalidraw 다이어그램
capture_smart_screenshot "$EXCALIDRAW_URL" "${FILENAME}-drops-카드-${COUNT}종" \
    "Excalidraw 다이어그램"

# 2~N. 각 항목별 스크린샷
for i in "${!ITEMS[@]}"; do
    IFS='|' read -r NUM NAME LINK AUTHOR DESC <<< "${ITEMS[$i]}"
    capture_smart_screenshot "$LINK" "${FILENAME}-drop-${NUM}" \
        "$NUM $NAME (by $AUTHOR)"
done

echo "✅ 이미지 $((COUNT+1))개 캡처 완료"
```

### Step 4: Drop 파일 생성 (정확한 속성 순서)

**파일명:** `04_studio/drops/${FILENAME}-drop-${DROP_NUMBER}-${슬러그}.md`

**Frontmatter (정확한 순서):**
```yaml
---
title: "Drop #${DROP_NUMBER} — ${DROP_TITLE}"
kind: drop
area: studio
status: draft
drop_status: ready
drop_number: "${DROP_NUMBER}"
type: ${DROP_TYPE}
tags:
  - type/drop
  - category/${CATEGORY}
source: 외부
link: 
memo: ${항목들 요약}
visibility: Member
created: ${TODAY}
published_at:
up: "[[MAP-드롭]]"
discord_message_id:
notion_page_id:
---
```

**콘텐츠 (동적 항목 개수):**
```markdown
## 공유 내용

📢 **이번 주 Drop ${COUNT}개 공개했어요!**

(항목별 반복):
**${NUM} ${NAME}** by ${AUTHOR}
${DESC}

![[${FILENAME}-drop-${NUM}.png|600]]

---

(마지막 항목 후):
더 자세한 내용은 각 Drop 문서를 확인해주세요! 🎯

## 링크

- ${NAME}: ${LINK}
(각 항목 반복)
```

### Step 5: MAP-드롭 그래프 링크 추가

Drop 파일명에서 wikilink를 생성해 `MAP-드롭.md`의 그래프 링크 callout 마지막 줄에 삽입한다.

```bash
MAP_FILE="_system/obsidian/maps/MAP-드롭.md"
DROP_SLUG="${FILENAME}-drop-${DROP_NUMBER}-${슬러그}"  # Drop 파일명에서 .md 제거

# 그래프 링크 callout의 마지막 항목 다음에 삽입
# "> - [[마지막항목]]" 패턴 찾아 그 뒤에 추가
python3 - <<PYEOF
import re

with open("$MAP_FILE", "r", encoding="utf-8") as f:
    content = f.read()

new_link = "> - [[$DROP_SLUG]]"

# 그래프 링크 callout 내 마지막 [[...]] 줄 다음에 삽입
pattern = r'(> - \[\[[^\]]+\]\]\n)(\n> \[!)'
replacement = r'\1' + new_link + r'\n\2'
updated = re.sub(pattern, replacement, content)

if updated == content:
    # fallback: callout 마지막 [[...]] 줄 바로 뒤에 추가
    pattern2 = r'((?:> - \[\[[^\]]+\]\]\n)+)'
    def add_link(m):
        block = m.group(1)
        return block.rstrip('\n') + '\n' + new_link + '\n'
    updated = re.sub(pattern2, add_link, content, count=1)

# updated 필드 갱신
import datetime
today = datetime.date.today().isoformat()
updated = re.sub(r'^updated:.*$', f'updated: {today}', updated, flags=re.MULTILINE)

with open("$MAP_FILE", "w", encoding="utf-8") as f:
    f.write(updated)

print(f"✅ MAP-드롭 그래프 링크 추가: [[$DROP_SLUG]]")
PYEOF
```

### Step 6: /drop-publish 자동 호출

```bash
echo "🚀 /drop-publish 스킬 호출..."
/drop-publish "04_studio/drops/${FILENAME}-drop-${DROP_NUMBER}*.md"
```

### Step 7: 칸반 자동 업데이트 (선택사항)

```bash
# 칸반 파일에서 항목 찾기 및 이동
KANBAN_FILE="_system/obsidian/kanban/칸반-드롭.md"

for i in "${!ITEMS[@]}"; do
    IFS='|' read -r NUM NAME LINK AUTHOR DESC <<< "${ITEMS[$i]}"
    
    # "🔜 발행 대기" → "✅ 발행 완료"로 항목 이동
    sed -i '' "/- \[ \] \[\(${NAME}\|${LINK}\)\]/s/^/✅ $TODAY  /g" "$KANBAN_FILE"
done
```

---

## 환경 설정 필수

**`_system/tools/.env`에 설정:**
```bash
DISCORD_BOT_TOKEN="your_bot_token"
DISCORD_GUILD_ID="your_guild_id"
DISCORD_SHARE_CHANNEL_ID="your_channel_id"
NOTION_API_KEY="your_api_key"
NOTION_DROPS_DB_ID="your_db_id"
```

---

## Drop 파일 속성 참조

기존 drop-0016 형식 따름:
- `title`: "Drop #NNNN — [제목]" (반드시 "#" 포함)
- `kind`: "drop" (고정)
- `area`: "studio" (고정)
- `status`: "draft" (생성 시) → "live" (발행 후)
- `drop_status`: "ready" (생성 시) → "live" (발행 후)
- `drop_number`: "NNNN" (4자리 zero-pad 문자열)
- `type`: "Tools", "News", "Research" 등
- `visibility`: "Member" (기본) 또는 "Internal"
- `author`: "김주용" (또는 다른 운영자)

---

## 완료 후 출력

```
✨ Drop #0017 공지 발행 완료!

📄 Drop 파일: 04_studio/drops/260401-drop-0017-도구4가지.md
🖼️  이미지 5개: 04_studio/assets/etc/
💬 Discord: https://discord.com/channels/.../1234567890
📋 Notion: https://www.notion.so/xxxxx

✅ /drop-publish 자동 실행 완료
   ✓ discord_message_id 기록됨
   ✓ notion_page_id 기록됨
   ✓ drops.csv 갱신됨
   ✓ 칸반 자동 이동 완료 (4개 항목)
```

---

## 주의사항

1. **항목 개수 유동적** — 1개~N개 모두 지원
2. **정밀 캡처** — URL 유형별로 스마트 영역 선택
3. **속성 순서 엄격** — drop-0016 형식 정확히 따름
4. **자동 발행** — `/drop-publish` 호출로 즉시 Discord+Notion 동시 발행
5. **네트워크 지연** — 각 사이트 로딩 4초 대기

---

## 확장 가능성

- **이미지 압축** — imagemagick으로 최적화
- **태그 자동 추론** — URL 분석해 category 자동 설정
- **멀티랭귀지** — 영문 요약 자동 생성
- **예약 발행** — `published_at` 미래 날짜로 설정 시 예약
- **칸반 정교화** — 칸반 frontmatter도 함께 업데이트
