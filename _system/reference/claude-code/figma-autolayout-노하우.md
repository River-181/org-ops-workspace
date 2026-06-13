---
title: Figma Plugin API — Auto Layout 핵심 노하우
status: live
created: 2026-06-13
kind: reference
area: system
up: "[[_system/reference/README]]"
tags:
  - type/reference
  - platform/figma
---

# Figma Plugin API — Auto Layout 핵심 노하우

> S01 공지 포스터 H스타일 v4 제작(2026-06-13) 과정에서 확립한 패턴.
> `mcp__claude_ai_Figma__use_figma` 기반 Plugin API 스크립트 작성 시 적용.

---

## 핵심 버그: TEXT 노드에 FILL 너비 금지

### 문제

```javascript
// 이렇게 하면 텍스트가 세로로 쌓임 (1자/줄)
txt.layoutSizingHorizontal = 'FILL'  // ❌ 비동기 — width가 0~1px로 붕괴
txt.textAutoResize = 'HEIGHT'
```

`layoutSizingHorizontal = 'FILL'`은 Auto Layout 컨텍스트에서 너비 계산이 비동기적으로 처리된다. `characters`를 먼저 설정하면 width가 아직 0~1px인 상태에서 줄바꿈이 일어나 텍스트가 한 글자씩 수직으로 렌더링된다.

### 해결

```javascript
function setTxt(node, fontName, size, chars, color, lineHt) {
  node.fontName = fontName
  node.fontSize = size
  if (lineHt) node.lineHeight = lineHt
  node.characters = chars        // 1. 텍스트 먼저
  node.resize(704, 500)          // 2. 명시적 너비(px) — FILL 금지
  node.textAutoResize = 'HEIGHT' // 3. 높이만 자동
  node.fills = [{type:'SOLID', color: hexRGB(color)}]
}
```

**핵심**: `resize(contentWidth, 임시높이)` + `textAutoResize = 'HEIGHT'` 조합. `characters` 이후에 호출해야 한다.

---

## RECT/FRAME 노드는 FILL 정상 동작

```javascript
// RECT에는 FILL 써도 됨
vis.layoutSizingHorizontal = 'FILL'
vis.layoutSizingVertical   = 'FILL'
```

이미지 비주얼 rect, 구분선 rect 등 TEXT가 아닌 노드에는 `FILL`이 동기적으로 반영된다.

---

## Spacer 패턴 (하단 고정)

```javascript
// 남은 공간을 채워 다음 요소를 하단으로 밀기
spacer.layoutSizingVertical = 'FILL'
```

**조건**: 부모 frame의 `primaryAxisSizingMode = 'FIXED'`여야 동작한다.  
부모가 HUG이면 FILL spacer가 의미 없음.

---

## Auto Layout 800×1200 프레임 구성

```javascript
frame.layoutMode                = 'VERTICAL'
frame.primaryAxisSizingMode     = 'FIXED'   // 1200px 고정
frame.counterAxisSizingMode     = 'FIXED'   // 800px 고정
frame.itemSpacing               = 0
frame.paddingLeft = frame.paddingRight = 0
frame.paddingTop  = frame.paddingBottom = 0
frame.resize(800, 1200)
```

섹션(headerSec, imgSec 등)의 padding은 섹션 단위로 설정한다.

---

## 콘텐츠 너비 계산

```
contentWidth = frameWidth - paddingLeft - paddingRight
             = 800 - 48 - 48
             = 704px
```

모든 텍스트 노드에 `resize(704, 500)`을 적용한다. 500은 임시 높이(후에 HEIGHT 자동조정됨).

---

## 헬퍼 함수 세트

```javascript
function hexRGB(h) {
  return {
    r: parseInt(h.slice(1,3), 16) / 255,
    g: parseInt(h.slice(3,5), 16) / 255,
    b: parseInt(h.slice(5,7), 16) / 255
  }
}

function isKor(c) {
  return (c >= 0xAC00 && c <= 0xD7AF)
      || (c >= 0x1100 && c <= 0x11FF)
      || (c >= 0x3130 && c <= 0x318F)
}

// 한국어 범위에 Noto Sans KR 적용 (혼용 텍스트)
function fixKor(node, str) {
  let i = 0
  while (i < str.length) {
    const k = isKor(str.charCodeAt(i))
    let j = i + 1
    while (j < str.length && isKor(str.charCodeAt(j)) === k) j++
    if (k) node.setRangeFontName(i, j, {family:'Noto Sans KR', style:'Regular'})
    i = j
  }
}

// // 주석 회색 처리
function greyComments(node, str) {
  let i = 0
  while (true) {
    const idx = str.indexOf('//', i)
    if (idx === -1) break
    let end = str.indexOf('\n', idx)
    if (end === -1) end = str.length
    node.setRangeFills(idx, end, [{type:'SOLID', color: hexRGB('#8A8480')}])
    i = end + 1
  }
}
```

---

## Style H 디자인 상수

```javascript
const C = {
  dark:  '#2C2825',
  grey:  '#8A8480',
  amber: '#D49A00',
  bg:    '#D4CFC4',
}
const SMREG = {family: 'Space Mono',    style: 'Regular'}
const NSKB  = {family: 'Noto Sans KR',  style: 'Bold'}
```

---

## 이미지 채우기 (업로드된 이미지 해시)

```javascript
vis.fills = [{
  type: 'IMAGE',
  imageHash: 'e4b572a3086ee2fd762c77c89fe75c60cb0d4da0',  // 미니피그 이미지
  scaleMode: 'FILL'
}]
```

이미지 해시는 `mcp__claude_ai_Figma__use_figma` 업로드 후 반환된 값 사용.

---

## 폰트 사이즈 교훈

**최소 12pt** 기준: 모노스페이스(Space Mono) 10pt는 인쇄/화면 모두 너무 작음.

| 용도 | 권장 크기 |
|------|----------|
| 영웅 제목 (NSK Bold) | 56pt |
| 섹션 제목 (NSK Bold) | 36pt |
| 본문 모노 | 12~13pt |
| 최소 | 12pt |
