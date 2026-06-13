---
name: studio-gallery
description: 스튜디오 코크핏 — 04_studio의 모든 시각 산출물·레퍼런스를 스캔해 studio-manifest.js를 생성/갱신하고, studio.html에서 라이브 갤러리·나란히 비교로 본다. "갤러리 갱신", "코크핏", "산출물 모아봐", "결과물 한눈에", 디자인 산출물 만든 직후 사용.
---

## 목적

04_studio 전체를 스캔해 HTML·이미지 산출물의 매니페스트를 생성하고, 서버 없이 브라우저 한 탭에서 라이브로 보고 나란히 비교한다.

## 실행

```bash
# 1. 매니페스트 생성/갱신
python3 04_studio/_cockpit/gen-manifest.py

# 2. 갤러리 열기 (파일 직접 열기)
open 04_studio/studio.html
```

## 언제 쓰나

- 포스터·이미지·HTML 산출물을 새로 만들거나 옮긴 직후 — 매니페스트를 재생성해야 갤러리에 반영된다.
- 여러 버전을 한눈에 비교하고 싶을 때 — "비교 모드"로 최대 4개 선택 후 나란히 보기.
- 특정 프로젝트·상태(final / iteration)의 산출물만 필터링해서 볼 때.

## 동작 원리

```
04_studio/ 스캔
    ↓
_cockpit/gen-manifest.py
    ↓  (stdlib만 사용, 서버 불필요)
studio-manifest.js  ← window.STUDIO_MANIFEST 전역 변수
    ↓
studio.html  <script src="studio-manifest.js">
    ↓  (fetch 없이 전역 변수로 읽음 — file:// CORS 우회)
iframe / img 타일 렌더
```

`fetch()`가 아닌 전역 변수 `.js` 파일을 쓰는 이유: `file://` 프로토콜에서는 fetch가 CORS 오류를 일으키므로 `<script src>` 방식으로 우회한다.

## 필터 옵션

| 필터 | 값 |
|------|-----|
| 유형 | 전체 / HTML / 이미지 |
| 모드 | 전체 / Light / Dark (HTML의 `data-mode` 속성 기준) |
| 상태 | 전체 / Final / Iteration / Standalone |
| 프로젝트 | 경로 기반 자동 추출 |
| 텍스트 | 이름·제목·경로 일치 |

## 문제 해결

**iframe이 `file://`에서 차단될 때** (브라우저 보안 정책):

```bash
cd 04_studio && python3 -m http.server 8800
# 그 다음 브라우저에서:
# http://localhost:8800/studio.html
```

**매니페스트가 없을 때**: studio.html이 안내 메시지와 함께 생성 명령을 표시한다.

## 관련

- [[260612-스튜디오-코크핏-설계]] — 아키텍처 설계 문서
- [[04_studio/projects]] — 디자인 프로젝트 루프
- [[studio-team]] — 산출물 생성 에이전트
