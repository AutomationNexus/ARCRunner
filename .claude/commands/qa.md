---
description: Run local QA checks and report pass/fail blockers.
argument-hint: [optional scope]
---

Dispatch `qa-gatekeeper`: `git status --short --branch` (confirm not `main`), verify the
`FROM` tag in `Dockerfile.runner` matches the workflow `TAG`/base reference in
`.github/workflows/build-runner-image.yml`, then `git diff --check`. Return pass/fail and
actionable blockers only. No file edits. Arguments: $ARGUMENTS
