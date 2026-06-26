---
description: Execute approved plan through build orchestrator and QA gate.
agent: build
---

Run the ARCRunner execute pipeline for an approved plan.

Steps:
- Run `git status --short --branch` and confirm a feature branch (not `main`). Create one from updated `main` if needed.
- Apply Dockerfile and workflow changes from the approved plan only.
- If bumping runner version, keep `Dockerfile.runner` `FROM` tag and workflow image tags in lockstep.
- Invoke `@arc-qa-gatekeeper` to run the full `/arc-qa` local gate.
- Stop on the first failed gate.
- Push the feature branch and open a PR to `main` after user approval (never push directly to `main`).

Return a compact handoff: commands run, pass/fail. Arguments: `$ARGUMENTS`.
