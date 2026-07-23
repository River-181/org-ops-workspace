---
name: drop-publish
description: 현재 Drop 파일을 Discord #share + Notion Drops DB에 발행하고 drops.csv를 갱신한다
argument-hint: "[파일경로 — 생략 시 현재 파일]"
allowed-tools: Bash
---

# /drop-publish — Drop 발행 스킬

## 목적

`drop_status: ready` 상태의 Drop 파일을 Discord + Notion에 발행한다.
내부 로직은 `_system/tools/publish-drop.py`가 처리 — LLM 개입 없이 직접 API 호출.

---

## 실행

```bash
python3 _system/tools/publish-drop.py <drop_파일_경로>
```

**예시**:
```bash
python3 _system/tools/publish-drop.py 04_studio/drops/260610-drop-0030-claude-fable5-mythos5.md
```

스크립트가 자동으로:
1. 환경변수 로드 (`_system/tools/.env`)
2. Discord #share에 카카오톡 공유용 텍스트 게시
3. 공유 내용 섹션 + 이미지를 스레드에 발행
4. Notion Drops DB에 페이지 생성
5. `drops.csv` 행 갱신 (discord_message_id, notion_page_id 포함)
6. Drop 파일 frontmatter 갱신 (`status: live`, `published_at`, IDs)
7. 카카오톡 공유용에 Discord 스레드 링크 삽입

멱등성 보장 — `drop_status: live`면 재실행해도 건너뜀.

---

## 사전 조건

Drop 파일 frontmatter:
```yaml
drop_status: ready      # ready 여야만 실행
```

카카오톡 공유용 섹션이 코드블럭으로 감싸져 있어야 함:
```
## 카카오톡 공유용

\`\`\`
[전송할 텍스트]
\`\`\`
```

**이미지 임베드는 두 가지 형식을 지원함** (`extract_gongyoo_sections()`가 슬래시 유무로 구분):
- `![[04_studio/assets/etc/파일명.png|700]]` — 전체 경로(볼트 루트 기준). 04_studio/assets/README.md 정본 규칙, 신규 드롭은 이 형식을 쓸 것.
- `![[파일명.png|700]]` — 바레 파일명. 드롭 파일과 같은 폴더(`04_studio/drops/`)에 이미지가 있을 때만 동작(구형 드롭 호환용).

임베드 경로에 실제 파일이 없으면 에러 없이 그냥 조용히 건너뛴다(`os.path.exists` 실패 시 무음 skip) — 발행 전 이미지 경로가 실제로 존재하는지 확인할 것.

---

## 오류 처리

| 상황 | 동작 |
|------|------|
| `drop_status` 가 ready 아님 | 중단 + 안내 |
| Discord API 실패 | 즉시 중단 (Notion 진행 안 함) |
| Notion API 실패 | 경고 출력, Discord ID는 저장 |
| 이미지 파일 없음 | 해당 이미지만 건너뜀 |

---

## 스크립트 경로

`_system/tools/publish-drop.py` — 수정이 필요하면 직접 편집.
