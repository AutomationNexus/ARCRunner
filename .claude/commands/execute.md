---
description: Execute an approved plan through the QA gate.
argument-hint: [optional focus notes]
---

Run the ARCRunner execute pipeline for an approved plan: $ARGUMENTS

1. `git status --short --branch` — confirm a feature branch (not `main`); create one from
   updated `main` if needed.
2. Apply Dockerfile and workflow changes from the approved plan only (only
   `Dockerfile.runner` and `.github/workflows/build-runner-image.yml` unless the user
   explicitly requests other paths).
3. If bumping the runner version, keep `Dockerfile.runner`'s `FROM` tag and the workflow's
   image tag in lockstep.
4. Dispatch `qa-gatekeeper` for the full `/qa` local gate.
5. Stop on the first failed gate.
6. Push the feature branch and open a PR to `main` after user approval (never push directly
   to `main`).
