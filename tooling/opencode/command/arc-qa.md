---
description: Run local QA checks and report pass/fail blockers.
agent: arc-qa-gatekeeper
---

Run local QA for this repo.

Steps:
- Run `git status --short --branch`.
- Confirm the current branch is **not** `main`.
- Verify the `FROM` tag in `Dockerfile.runner` matches the workflow `TAG` / base reference in `.github/workflows/build-runner-image.yml`.
- Run `git diff --check`.

Return pass/fail and actionable blockers only. Do not edit files. Arguments: `$ARGUMENTS`.
