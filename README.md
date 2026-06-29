# ci-workflows

Reusable GitHub Actions workflows owned by Yura, consumed by student **project** working repos.

## `new-code-quality.yml`

Diff-aware **"Clean as You Code"** quality gate. Runs on each PR of a working repo and reports
only on the code the PR introduces (not the project's absolute state):

- **Tests** + **diff coverage** (`diff-cover` of the lcov vs. the base branch)
- **Semgrep diff-aware** (`--baseline-commit origin/<base_ref>`, new findings only)

It publishes a structured `check_run` named `yura/new-code-report` (conclusion `neutral`) using the
Actions `GITHUB_TOKEN`. The Yura backend ingests that `check_run`, computes blocking severity
server-side (green/amber/red, per-course config) and posts the separate `yura/new-code` commit
status + a PR comment. **Severity is decided by Yura, not by this workflow.**

### Usage

Seeded into each working repo as `.github/workflows/yura-quality.yml`:

```yaml
name: Yura Code Quality
on:
  pull_request:
    types: [opened, synchronize, reopened]
permissions:
  checks: write
  contents: read
  pull-requests: write
jobs:
  quality:
    uses: yura-edu/ci-workflows/.github/workflows/new-code-quality.yml@v1
    with:
      language: typescript
      test_command: 'npm test -- --coverage'
      base_ref: develop
```

Pinned to **`@v1`**.
