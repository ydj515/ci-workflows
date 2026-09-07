# Reusable Workflows Catalog

이 저장소는 공통으로 재사용하는 GitHub Actions 워크플로 카탈로그입니다.
소비 저장소에서 `uses:` 구문으로 호출해 사용합니다.

## dev-standards와의 관계

- `dev-standards`: 개발 표준, AI 가이드 문서 및 설정 템플릿의 단일 원본
- `ci-workflows`: 표준을 소비 저장소에 배포하고 동기화하는 재사용 워크플로

## 워크플로 카탈로그

### Sync Development Standards

- 목적: `.dev-standards/config.yml`을 기준으로 병합 styleguide, 개별 표준 및 선택적인
  프로젝트 설정을 동기화
- 위치: `.github/workflows/sync-dev-standards.yml`
- 트리거: `workflow_call`, `workflow_dispatch`
- 필수 입력: `standards_owner`
- 권한: `contents: write`
- 주요 산출물: `.dev-standards/styleguide.md`, `.dev-standards/standards/**`, 선택 시
  `.gemini/styleguide.md`

[상세 사용 가이드](docs/sync-dev-standards.md)

`uses`의 버전과 `standards_ref`의 차이는
[두 버전 ref의 의미](docs/sync-dev-standards.md#두-버전-ref의-의미)를 참고합니다.

복사 가능한 예시:

- [상시 동기화 workflow](examples/dev-standards/sync-dev-standards.yml)
- [최초 bootstrap workflow](examples/dev-standards/bootstrap-dev-standards.yml)
- [React 설정](examples/dev-standards/react-config.yml)
- [Spring/Gradle 설정](examples/dev-standards/spring-gradle-config.yml)

### Notify Slack when Gemini review is done

- 목적: Gemini Code Assist PR 리뷰 완료 시 Slack 알림 전송
- 위치: `.github/workflows/gemini-pr-review-slack-noti.yml`
- 트리거: `pull_request_review` (`submitted`)
- 필수 시크릿: `SLACK_REVIEW_WEBHOOK_URL`

[호출 workflow 예시](examples/gemini-pr-review-slack-notification.yml)
