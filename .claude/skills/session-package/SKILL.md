---
name: session-package
description: 세션 자료를 member-share-space에 멤버 배포용으로 패키징한다 — 공유 폴더 생성, 파일 복사, 네이밍 통일, README 작성
argument-hint: "[세션ID]"
---

# session-package

세션 자료를 `02_sessions/member-share-space/`에 멤버 배포용 패키지로 구성한다.
세션 전·중·후 언제든 실행 가능하며, 파일 원본은 `materials/`에 그대로 유지한다.

---

## 입력

- **세션 ID**: `NN` 또는 `S{NN}` (예: `01`, `S01`)
- 인자 없으면 가장 최근 세션 폴더를 감지하거나 사용자에게 묻는다.

---

## 절차

### 1. 컨텍스트 파악

```bash
# 세션 폴더 찾기
ls 02_sessions/ | grep "S{NN}"
```

`02_sessions/S{NN}-{슬러그}/S{NN}-{슬러그}-plan.md` 읽기:
- 세션 실시일 → `YYYYMMDD` (created가 아닌 세션 날짜)
- 세션 제목 (한글, 짧게)
- 진행자, 장소, 시간, 아젠다 구조

hub 파일 `02_sessions/S{NN}-{슬러그}/S{NN}-{슬러그}.md` 읽기:
- 슬라이드 URL, Google Drive 링크, [녹음 앱] 링크 등 배포용 링크 목록

### 2. 공유 폴더 결정

**폴더명 형식**: `YYYYMMDD | [클럽명] | S{NN} | {제목} | 자료`

예시: `20260530 | [클럽명] | S01 | [세션명] | 자료`

폴더가 이미 존재하면:
- 기존 파일 목록을 사용자에게 보여준다
- 파일 추가/갱신을 계속할지 확인한다

```bash
SHARE_DIR="02_sessions/member-share-space/YYYYMMDD | [클럽명] | S{NN} | {제목} | 자료"
mkdir -p "${SHARE_DIR}"
```

### 3. 파일 복사 + 네이밍 통일

원본은 `materials/`에 그대로 유지 — **이동(mv) 금지, 복사(cp)만 사용**.

**네이밍 규칙**:

| 자료 유형 | 파일명 패턴 |
|---------|-----------|
| 1부 슬라이드 MD | `YYMMDD-S{NN}-part1-{슬러그}.md` |
| 1부 슬라이드 ZIP | `YYMMDD-S{NN}-part1-{슬러그}.zip` |
| 2부 슬라이드 PPT/ZIP | `YYMMDD-S{NN}-part2-{슬러그}.ext` |
| 번외 자료 (Discord 가이드 등) | `YYMMDD-S{NN}-part3-{슬러그}.ext` |
| 녹음본 원문 ([녹음 앱] MD) | `YYMMDD-S{NN}-세션녹음본.md` |
| 정제 스크립트 | `YYMMDD-S{NN}-세션스크립트.md` |
| 세션 요약 | `YYMMDD-S{NN}-세션정리.md` |

**배포 포함 대상** — materials/ 중:
- 슬라이드 MD 원문 (`*원문*`, `*슬라이드*.md`)
- PPT/PPTX/ZIP (발표 패키지)
- HTML (Discord 온보딩 등 단일 파일)
- [녹음 앱] 녹음본 MD
- 이미 생성된 정제 스크립트·세션 정리 파일

**배포 제외 대상**:
- `materials/research/` 하위 내부 리서치 노트
- `materials/assets/` 하위 슬라이드 이미지
- `materials/slides/` 하위 HTML 덱 폴더 (→ ZIP으로 묶어서 포함)
- `materials/_unused/` 하위 파일
- `*-production-context.md`, `*-claude-design-brief.md` 등 내부 작업 문서

HTML 덱 폴더는 ZIP으로 묶어 복사:
```bash
cd materials/slides/
zip -r "../../{SHARE_DIR}/YYMMDD-S{NN}-part1-{슬러그}-덱.zip" "{덱폴더명}/"
```

### 4. README.md 생성/갱신

`{SHARE_DIR}/README.md`를 생성하거나 갱신한다.

**형식** (마크다운 헤더 없이 플레인텍스트 스타일, 기존 S01 패턴 준수):

```
[ S{NN} — {제목} ]
{시작시간}–{종료시간} | {장소}

[ 개괄 ]
{plan.md의 한 줄 소개}

[ 아젠다 ]
1부 · {1부 제목} ({진행자} · {시간})
2부 · {2부 제목} ({진행자} · {시간})
번외 · {번외 항목} ({시간})

[ 준비물 ]
{준비물 목록}

[ 자료 ]
전체 자료
{Google Drive 링크}

세션 녹음본 (비밀번호: {비밀번호})
{[녹음 앱] 링크}

1부 슬라이드
{Netlify 또는 Drive 링크}

2부 슬라이드 + 실습
{Drive 링크}

{기타 섹션들}
```

링크를 알 수 없는 항목은 `[링크 미확정]`으로 표기한다.

### 5. 결과 보고

```
✅ session-package 완료 — S{NN}

📁 공유 폴더:
  02_sessions/member-share-space/{폴더명}/

📄 복사된 파일:
  {파일1} ← {원본 경로}
  {파일2} ← {원본 경로}

⚠️ 플레이스홀더 남은 항목:
  {링크 미확정 항목 목록}

💡 다음 단계:
  - /session-transcript S{NN}  (녹음본 있으면)
  - /session-close S{NN}       (세션 종료 후)
```

---

## 주의사항

- HTML 슬라이드 덱 폴더(여러 파일)는 ZIP으로 묶어 복사한다
- Google Drive 동기화로 자동 전파되므로 저장만으로 멤버 공유 완료
- 녹음본 원문은 그대로 복사 — 정제 작업은 `/session-transcript`가 담당
- `session-transcript` 산출 파일이 아직 없으면 나중에 다시 실행해 추가한다

---

## 연관 스킬

| 스킬 | 관계 |
|------|------|
| `/session-transcript {세션ID}` | 정제본·요약본 생성 → 이 스킬로 공유 폴더에 추가 |
| `/session-close {세션ID}` | 세션 종료 후 MOC·링크 갱신 |
| `/session-ops record {세션폴더}` | record.md 작성 |
