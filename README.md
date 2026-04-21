# probe-log

Probe 공개 원문 아카이브입니다.

## 목적

- `Probe_Obsidan/content/` 의 public-safe longform을 외부 canonical로 축적
- 카드/이미지 CDN 용도인 `probe-assets` 와 분리 운영
- raw diary가 아니라 이미 번역된 공개용 원문만 보관

## 공개 주소

- repo: `https://github.com/probe-web/probe-log`
- site: `https://probe-web.github.io/probe-log/`

## 현재 상태

- `content/manifest.json` 기준 published EP `10편`이 archive에 반영돼 있다
- episode page canonical은 `https://probe-web.github.io/probe-log/episodes/ep*/` 형식이다
- unpublished draft는 `probe-log`에 두지 않는다
- 정상 운영에서는 `dashboard.py` Cards 탭의 `Publish 4ch` 뒤에 공개 archive 반영이 자동으로 이어진다

## 기본 운영 흐름

1. authoring 원문은 기존 workspace에서만 관리한다
2. 수한이가 `dashboard.py` Cards 탭에서 `Publish 4ch`를 실행한다
3. `scripts/publish.py` 가 4채널 발행과 `content/manifest.json` 갱신을 수행한다
4. 대상이 published `probe_log` EP면 allowlist 검증 뒤 public archive까지 자동 반영한다

사용자가 별도로 public repo 반영용 파이썬 명령을 실행할 필요는 없다.

## 내부 동기화 도구

published archive는 `content/manifest.json` 기준으로 다시 만든다.

```bash
python3 scripts/sync_probe_log.py
```

이 스크립트는:

- `probe-log/episodes/ep*.md` 를 published 상태로 refresh
- `probe-log/index.md` 를 재생성
- `content/manifest.json`의 `canonical_url`을 archive URL로 갱신

## 공개 배포 규칙

public repo는 작업 트리를 직접 push하지 않는다.

```bash
python3 scripts/publish_probe_log_public.py --output-dir /tmp/probe-log-public-check
```

실제 공개 push는 allowlist 검증을 통과한 번들만 사용한다.

```bash
python3 scripts/publish_probe_log_public.py --push --force
```

위 명령은 사용자용 기본 절차가 아니라 Probe 내부 점검/복구용 fallback이다.

이 스크립트는:

- `sync_probe_log.py`를 먼저 실행한다
- 허용된 파일만 새 임시 번들에 다시 조립한다
- `episodes/ep*.md` 외 다른 파일이 섞이면 실패한다
- 민감 패턴이 보이면 실패한다
- 필요하면 public repo를 검증된 번들로만 갱신한다

## 운영 원칙

- social 발행 전 원문은 기존 authoring workspace에서만 관리한다
- social 발행 후 canonical 원문만 `episodes/` 로 올린다
- `probe-log`는 published archive만 담는다
- raw diary 표현은 옮기지 않는다
- public repo 갱신은 자동 발행 루틴 또는 `publish_probe_log_public.py` fallback만 사용한다
