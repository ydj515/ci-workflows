# ci-workflows

여러 저장소에서 공통으로 사용할 GitHub Actions workflow를 버전별로 제공하는 카탈로그입니다.

소비 저장소는 workflow 구현을 복사하지 않고 `uses:`로 호출합니다. 각 저장소는 trigger,
permission과 입력값만 소유하고, 공통 checkout·생성·전달 절차는 이 저장소에서 관리합니다.

## 제공 Workflow

| Workflow | 역할 |
| --- | --- |
| `sync-dev-standards.yml` | `dev-standards` Release를 조합해 소비 저장소에 pull request로 전달 |
| `gemini-pr-review-slack-noti.yml` | Gemini Code Assist의 pull request review 완료를 Slack으로 알림 |

## 빠른 시작

다음 workflow는 매주 월요일 최신 `dev-standards` Release를 확인하고 변경사항이 있을 때 pull
request를 생성합니다.

```yaml
name: Sync Development Standards

on:
  workflow_dispatch:
  schedule:
    - cron: "15 0 * * 1"

permissions:
  contents: write
  pull-requests: write

jobs:
  sync:
    uses: ydj515/ci-workflows/.github/workflows/sync-dev-standards.yml@v1.2.3
    with:
      standards_owner: ydj515
      standards_repo: dev-standards
      standards_ref: latest-release
      delivery_mode: pull-request
```

소비 저장소는 `.dev-standards/config.yml`에서 적용할 언어, 프레임워크, 아키텍처와 도구를
선택합니다. 전체 설치 과정과 pull request 권한 설정은
[Development Standards 동기화 가이드](docs/sync-dev-standards.md)를 참고합니다.

## 버전 정책

- `uses: ...@v1.2.3`은 이 저장소의 workflow 구현과 입력 계약을 고정합니다.
- `standards_ref: latest-release`는 실행 시점의 최신 `dev-standards` 정식 Release를 선택합니다.
- 재현성이 필요한 환경은 두 값을 각각 tag 또는 commit SHA로 고정합니다.

두 저장소의 버전은 독립적입니다. workflow 동작을 변경할 때는 `uses` ref를, 개발 표준을
변경할 때는 `standards_ref`를 갱신합니다.

## 문서

- [문서 전체 보기](docs/README.md)
- [Development Standards 동기화](docs/sync-dev-standards.md)
- [복사 가능한 예시](examples/dev-standards/)

## 개발

변경 후 YAML 구문과 Git diff를 확인합니다.

```sh
ruby -ryaml -e 'Dir["{.github/workflows,examples}/**/*.yml"].each { |path| YAML.load_file(path) }'
git diff --check
```
