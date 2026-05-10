# Reusable Workflows Catalog

이 저장소는 여러 재사용 워크플로를 제공합니다. 아래 카탈로그에 있는 워크플로를
필요한 레포지토리에서 `uses:`로 호출해 사용하세요.

## dev-standards와의 관계

이 저장소는 공통 규칙 문서의 원본을 직접 보관하기보다,
`dev-standards` 저장소에 버전별로 관리되는 문서를 각 레포지토리에 전달하는 역할을 담당합니다.

특히 `build-gemini-styleguide.yml`은 아래 흐름으로 동작합니다.

1. 호출 레포의 `.gemini/styleguide.yml`을 읽습니다.
2. `dev-standards`의 `base`, `language`, `framework` 문서를 `standards_ref` 기준으로 가져옵니다.
3. 조합된 결과를 `.gemini/styleguide.md`로 생성하고 필요 시 커밋합니다.

즉, `dev-standards`는 규칙의 원본 저장소이고,
`ci-workflows`는 그 규칙을 소비 레포지토리에 배포하는 reusable workflow 저장소입니다.

## 워크플로 카탈로그

### Build Gemini Styleguide

- 목적: `.gemini/styleguide.yml` 기반으로 `.gemini/styleguide.md` 생성/동기화
- 위치: `.github/workflows/build-gemini-styleguide.yml`
- 트리거: `workflow_call`
- 입력값:
  - `standards_owner` (필수)
  - `standards_repo` (기본값: `dev-standards`)
  - `standards_ref` (기본값: `v1.1.0`)
- 권한: `contents: write` 필요
- 출력물: `.gemini/styleguide.md` (변경 시 커밋/푸시)
- 추가 설정:
  - 호출 레포에 `.gemini/styleguide.yml` 필요
  - `standards_ref`로 `dev-standards`의 태그 또는 브랜치를 지정 가능

예시 1) 스타일 가이드 설정 파일

```yaml
# .gemini/styleguide.yml
language: java
frameworks:
  - spring
  - jpa
```

예시 2) 호출용 워크플로

```yaml
name: Sync Gemini Styleguide

on:
  workflow_dispatch:
  push:
    paths:
      - '.gemini/styleguide.yml'
  schedule:
    # 매일 09:05 KST = 00:05 UT
    - cron: "5 0 * * *"

permissions:
  contents: write

jobs:
  sync-guide:
    uses: your-org/ci-workflows/.github/workflows/build-gemini-styleguide.yml@v1.0.0 # ci-workflows tag를 의미
    with:
      standards_owner: your-org
      standards_repo: dev-standards
      # standards_ref: v1.1.0 (기본값) # dev-standards tag를 의미
```

### Notify Slack when Gemini review is done

- 목적: Gemini 리뷰 완료 시 Slack 알림 전송
- 위치: `.github/workflows/gemini-pr-review-slack-noti.yml`
- 트리거: `pull_request_review` (submitted)
- 필수 시크릿:
  - `SLACK_REVIEW_WEBHOOK_URL`

예시 1) 레포에 추가할 워크플로

```yaml
name: Notify Slack when Gemini review is done

on:
  workflow_dispatch:
  pull_request_review:
    types: [submitted]

jobs:
  notify-slack:
    uses: your-org/ci-workflows/.github/workflows/gemini-pr-review-slack-noti.yml@v1.0.0
    secrets: inherit
```

예시 2) 필요한 시크릿

- `SLACK_REVIEW_WEBHOOK_URL`

## 참고

- `standards_owner`는 필수 입력입니다.
- `standards_repo`와 `standards_ref`는 기본값이 있습니다.
- `standards_ref`는 `dev-standards` 저장소의 태그 또는 브랜치를 의미합니다.
- 호출 시에는 버전 태그(`v1.0.0`)처럼 고정된 태그 사용을 권장합니다.
- `.gemini/config.yaml`은 존재할 때만 커밋됩니다.

## 태그 전략

- 배포는 `v1.0.0`처럼 고정 버전 태그로 합니다.
- 필요하면 `v1` 별칭 태그를 최신 `1.x`에 맞춰 함께 유지합니다.
- 호출 레포는 안정성이 필요하면 `v1.0.0`, 빠른 반영이 필요하면 `v1`을 사용합니다.
