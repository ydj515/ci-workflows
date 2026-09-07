# Repository Guidelines

## Project Structure & Module Organization
This repository is a catalog of reusable GitHub Actions workflows.
- `.github/workflows/sync-dev-standards.yml`: Validates `.dev-standards/config.yml`, resolves default architecture profiles, syncs a merged styleguide and selected individual standards, and can explicitly bootstrap non-overwriting configuration files.
- `.github/workflows/gemini-pr-review-slack-noti.yml`: Sends a Slack notification when Gemini Code Assist submits a PR review.

## Development & Validation
- Validate YAML syntax:
  ```sh
  python3 -c "import yaml; yaml.safe_load(open('.github/workflows/sync-dev-standards.yml'))" 2>/dev/null || true
  ```
- Check git formatting:
  ```sh
  git diff --check
  git status --short
  ```
