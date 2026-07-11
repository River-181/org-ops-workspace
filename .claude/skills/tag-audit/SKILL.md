---
name: tag-audit
description: 태그 일관성 진단 — 미등록 태그, 네임스페이스 위반, 사이드카 규약 위반 보고
allowed-tools: Read, Bash
---

# 태그 감사

## 목적

볼트 전체의 태그 사용 현황을 수집하고 태그 레지스트리와 비교하여 위반 사항을 보고한다. **파일을 수정하지 않는다.**

## 등록 네임스페이스 (현행 13개)

```
ops/, session/, project/, research/, library/,
platform/, role/, season/, studio/, style/,
type/, drop/, system/
```

정본: `_system/obsidian/tag-registry.md`

## 파일 분류

| 구분 | 판별 기준 | 태그 규약 |
|------|----------|----------|
| 일반 .md | `*.md` (사이드카 아님) | 레지스트리 등록 태그만 |
| 사이드카 .md | `*.jpeg.md`, `*.jpg.md`, `*.png.md`, `*.webp.md`, `*.gif.md` | `studio/*`, `style/*`, `session/*`만 허용 |
| 아카이브 | `05_library/seasons/2025-pre/` | 감사 제외 |

## 감사 스크립트

아래 스크립트를 `Bash`로 실행한다.

```python
import os, yaml, re

VAULT = '.'
reg_content = open('_system/obsidian/tag-registry.md').read()
registered = set(re.findall(r'`([^`]+)`', reg_content))

# 사이드카 허용 네임스페이스
SIDECAR_NS = ('studio/', 'style/', 'session/')
SIDECAR_PAT = re.compile(r'\.(jpeg|jpg|png|webp|gif)\.md$')

results = {'unreg': [], 'sidecar_violation': [], 'no_tags': [], 'ok': 0}

for root, dirs, files in os.walk(VAULT):
    dirs[:] = [d for d in dirs if d not in ('.git', '.trash')
               and '2025-pre' not in os.path.join(root, d)]
    for fn in files:
        if not fn.endswith('.md'):
            continue
        path = os.path.join(root, fn)
        rel = path[2:]
        is_sidecar = bool(SIDECAR_PAT.search(fn))
        content = open(path, errors='ignore').read()
        if not content.startswith('---'):
            continue
        end = content.find('\n---', 3)
        if end == -1:
            continue
        try:
            data = yaml.safe_load(content[3:end]) or {}
        except:
            continue
        tags = data.get('tags') or []
        if isinstance(tags, str):
            tags = [tags]
        if not tags:
            results['no_tags'].append(rel)
            continue
        if is_sidecar:
            bad = [t for t in tags if not any(t.startswith(ns) for ns in SIDECAR_NS)]
            if bad:
                results['sidecar_violation'].append((rel, bad))
        else:
            bad = [t for t in tags if t not in registered]
            if bad:
                results['unreg'].append((rel, bad))
            else:
                results['ok'] += 1

print(f"정상: {results['ok']}개")
print(f"미등록 태그: {len(results['unreg'])}개 파일")
for p, t in results['unreg']:
    print(f"  {p}: {t}")
print(f"사이드카 규약 위반: {len(results['sidecar_violation'])}개")
for p, t in results['sidecar_violation']:
    print(f"  {p}: {t}")
print(f"태그 없음: {len(results['no_tags'])}개")
```

위 스크립트를 `Bash`로 실행:
```bash
python3 - <<'EOF'
# (스크립트 붙여넣기)
EOF
```

## 결과 판정 기준

| 심각도 | 조건 |
|--------|------|
| CRITICAL | 미등록 태그 또는 사이드카 규약 위반 |
| WARNING | 태그 없는 활성 .md 파일 |
| INFO | 레지스트리에는 있지만 볼트에서 미사용 태그 |

## 레지스트리 갱신 제안

미등록 태그 중 올바른 네임스페이스를 가진 것은 `/tag-register` 스킬로 등록한다.

## 결과 저장

`_system/obsidian/audits/YYMMDD-tag-audit.md`에 저장한다.

필수 frontmatter:
```yaml
source_skill: tag-audit
kind: audit
area: system
up: "[[MAP-시스템]]"
```

## 주의사항

- 태그 수정은 `frontmatter-doctor` 에이전트가 수행한다.
- 사이드카 규약: `studio/*` + `style/*` 허용. `character:` 필드가 이미 있으므로 캐릭터명 태그 불필요.
- 정본 규약: `04_studio/brand/lego/프롬프트-사이드카-규약.md`
