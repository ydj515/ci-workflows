# Repository Guidelines

## Project Structure & Module Organization

This repository is a catalog of reusable GitHub Actions workflows. The main
documentation lives in `README.md`, and workflow definitions are stored under
`.github/workflows/`.

- `.github/workflows/build-gemini-styleguide.yml`: builds and syncs
  `.gemini/styleguide.md` from a caller repository's `.gemini/styleguide.yml`.
- `.github/workflows/gemini-pr-review-slack-noti.yml`: sends a Slack notification
  when Gemini Code Assist submits a pull request review.
- `.editorconfig`: enforces UTF-8, LF line endings, and a final newline.

There is no application source tree, package manifest, or local asset pipeline in
this repository. Treat workflow files as the primary source code.

## Build, Test, and Development Commands

This project has no package manager build step. Validate changes with lightweight
Git and YAML checks before opening a pull request.

```sh
git status --short
```

Shows modified and untracked files.

```sh
ruby -e "require 'yaml'; Dir['.github/workflows/*.yml'].each { |f| YAML.load_file(f); puts f }"
```

Parses workflow YAML files and prints each valid file path.

```sh
git diff --check
```

Detects whitespace errors and missing final newlines.

## Coding Style & Naming Conventions

Use two-space indentation for YAML. Keep workflow names descriptive and
action-oriented, such as `Build Gemini Styleguide` or `Notify Slack when Gemini
review is done`. Prefer lowercase, hyphenated workflow filenames ending in
`.yml`, for example `build-gemini-styleguide.yml`.

Follow the existing style: concise step names, explicit `permissions`, and
uppercase environment variables such as `DEV_STANDARDS_REF` and
`SLACK_REVIEW_WEBHOOK_URL`.

## Testing Guidelines

There is no automated test suite. For workflow changes, manually review the
trigger, required inputs, secrets, permissions, and generated outputs. When
editing `workflow_call` inputs, update `README.md` examples in the same pull
request. For shell blocks, check that variables are quoted where needed and that
failure behavior is intentional.

## Commit & Pull Request Guidelines

Recent commits use short conventional prefixes such as `fix:`, `build:`, and
`chore:`. Keep commit messages concise and imperative, for example
`fix: update framework guide fetch logic`.

Pull requests should include a brief summary, affected workflow paths, validation
commands run, and any required caller repository changes. Link related issues
when available. If Slack text, generated guide output, or workflow behavior
changes, include a before/after snippet.

## Security & Configuration Tips

Do not commit real webhook URLs, tokens, or organization secrets. Reference
secrets through GitHub Actions contexts, for example
`${{ secrets.SLACK_REVIEW_WEBHOOK_URL }}`. Pin external workflow or standards
references with stable tags such as `v1.0.0` when documenting usage.
