---
name: slide-prep
description: 슬라이드 MD 파일을 외주자/AI 툴이 바로 사용할 수 있는 완성본으로 만든다
argument-hint: "[슬라이드 파일 경로]"
---

# slide-prep

## 목적

슬라이드 콘텐츠 MD 파일에 **디자인 스펙 + 이미지 가이드 + 다이어그램 프롬프트**를 추가하여
외주자 또는 NotebookLM/Figma Make AI 등 AI 제작 툴에 바로 넘길 수 있는 완성본을 만든다.

## 입력

- **파일 경로**: 대상 슬라이드 MD 파일 (예: `02_sessions/S01-.../materials/슬라이드.md`)

인자가 없으면 사용자에게 경로를 물어본다.

## 절차

### 0. 스타일 선택 (가장 먼저)

슬라이드는 `모드 × 타입 × **스타일**` 3축이다. **덱 단위로 스타일 하나**를 먼저 고른다. 스타일이 정해지면 기본 모드는 자동으로 따라오므로 모드를 따로 묻지 않는다.

| 용도 | 스타일 | 기본 모드 |
|------|--------|----------|
| 세션·기술 발표 (메인) | 스탠다드 | Dark Orbit |
| 개념·워크숍·아이스브레이킹 | 손그림 | Light Desk |
| 코드·문서형 딥다이브 | 미니멀 모노 | Dark / Light Desk |
| 외부 소개·OT·모집 | 브랜드 라이트 | Light Brand |
| 시즌 킥오프·비전·임팩트 | 레고 (D/G/H만) | 혼합 (R3) |

결정 트리·상세는 정본 `04_studio/brand/system/recipes/slide-styles.md` 참조.

> **손그림 분기**: 손그림 스타일이면 이 스킬의 HTML 디자인 스펙 절차를 따르지 않고, Obsidian Excalidraw 템플릿 `04_studio/excalidraw/templates/슬라이드-손그림.excalidraw.md`를 복제해 프레임별로 작성한다.

### 1. 파일 현황 파악

파일을 읽어 다음을 확인한다:
- 슬라이드 총 장 수, 파트 구조
- 디자인 스펙 섹션 유무 (`## 디자인 스펙`)
- 기존 콜아웃(`[!tip]`, `[!example]`) 삽입 여부
- `[링크]` 같은 미입력 플레이스홀더 위치

### 2. 디자인 스펙 섹션 추가 (없을 경우)

모드는 Step 0에서 고른 스타일의 기본 모드를 따른다(스탠다드·미니멀모노=Dark, 브랜드라이트=Light, 손그림=Light Desk·별도 분기, 레고=혼합). 콘텐츠 대비상 변경이 필요할 때만 사용자에게 확인.

파일 상단 본문 시작 직전에 삽입 (Dark 모드 기본값 — Light면 괄호 안 값으로 교체):

```markdown
## 디자인 스펙

| 항목 | 값 |
|------|-----|
| 규격 | 1920 × 1080px (16:9) |
| 모드 | Dark (세션/기술) ← Light(브랜드/외부)로 변경 가능 |
| 배경 | `var(--bg)` — Dark: `#0C0B10` / Light: `#F4F1EA` |
| 강조색 | `var(--accent)` — Dark: `#8B74E8` / Light: `#6F56D8` |
| 보조색 | `var(--[클럽명]-accent-blue)` `#BFD8F5` |
| 제목 폰트 | `var(--[클럽명]-font-display)` Noto Serif KR Bold |
| 본문 폰트 | `var(--[클럽명]-font-body)` Pretendard Regular / Medium |
| 영문 UI 폰트 | `var(--[클럽명]-font-ui)` Inter |
| 코드 폰트 | `var(--[클럽명]-font-code)` JetBrains Mono |

> 토큰 전체: `04_studio/brand/system/design-tokens.css` (SSOT)

### 슬라이드 유형별 스타일

| 유형 | 설명 |
|------|------|
| 섹션 타이틀 | 중앙 정렬, 대형 제목, 배경 이미지/그라디언트 |
| 콘텐츠 | 왼쪽 텍스트 + 오른쪽 비주얼 (60/40) |
| 풀 이미지 | 배경 전체 이미지, 텍스트 오버레이 |
| 다이어그램 | 다이어그램 중심, 캡션 최소화 |
| 클로징 | 섹션 타이틀과 동일 포맷 |

### 파트 구조

{슬라이드 파트 구조를 파일 내용에서 파악하여 여기에 채운다}
```

### 3. 이미지 가이드 콜아웃 삽입

각 슬라이드를 검토하여 이미지 활용이 유효한 슬라이드 바로 아래에 삽입:

```markdown
> [!tip] 📸 이미지 가이드
> - **배경**: Unsplash 검색어: `{검색어}`
> - **배치**: {위치 설명}
> - **출처**: {Unsplash / 직접 촬영 / 스크린샷}
```

**삽입 기준:**

| 슬라이드 성격 | 이미지 유형 |
|-------------|-----------|
| 감성·무드 오프닝/클로징 | Unsplash 배경 이미지 |
| 도구 소개 (Notion, Obsidian 등) | 실제 스크린샷 |
| 진행자 소개 | 진행자 사진 |
| 장소·현장 분위기 | 공간 사진 |
| 개념 설명 텍스트만으로 충분 | 생략 |

### 4. Gemini 다이어그램 프롬프트 삽입

관계·구조·흐름이 복잡한 슬라이드 바로 아래에 삽입:

```markdown
> [!example]- 🤖 Gemini 다이어그램 프롬프트
> ```
> Create a {diagram type} for a presentation slide.
> Canvas: 1920×1080px, dark background #0C0B10 (var(--bg) Dark).
> {구조 설명 — 레이어, 노드, 흐름 등}
> Color palette: purple #8B74E8 (var(--accent)), blue #BFD8F5 (var(--[클럽명]-accent-blue)), white #FFFFFF.
> Style: clean, minimal, modern. No decorative elements. Font: Inter.
> ```
```

**삽입 기준 — 다이어그램이 필요한 슬라이드 유형:**
- 레이어드 아키텍처 (PKM 스택, 시스템 구조)
- 관계 다이어그램 (팬인/팬아웃, 버블차트)
- 타임라인·로드맵
- 비교 구조 (툴 비교, 방법론 비교)

텍스트·불릿으로 충분한 슬라이드는 다이어그램 생략.

### 5. 플레이스홀더 교체

`[링크]`, `[URL]`, `TBD` 등 미입력 항목을 확인하여 사용자에게 실제 값을 요청하거나,
알고 있으면 바로 교체한다.

### 6. 완성 확인 리포트

작업 후 다음 표를 사용자에게 보여준다:

| 항목 | 상태 |
|------|------|
| 디자인 스펙 섹션 | ✓ / 추가됨 |
| 이미지 가이드 콜아웃 | N개 슬라이드 |
| Gemini 프롬프트 콜아웃 | N개 슬라이드 |
| 플레이스홀더 교체 | ✓ / N개 미처리 |

## 참고

- 디자인 SSOT: `04_studio/brand/system/DESIGN_SYSTEM.md` (모드 결정 트리 포함)
- 토큰 전체: `04_studio/brand/system/design-tokens.css`
- 슬라이드 레시피: `04_studio/brand/system/recipes/slide.md` (색상 마이그레이션 v1→v2 표 포함)
- 사례 파일: `02_sessions/S00-OT/materials/클럽소개-슬라이드.md`
