---
description: Verify PR readiness with local QA and branch policy.
agent: arc-qa-gatekeeper
---

Check whether the repo is ready to open or update a PR to `main`.

Steps:
- Confirm the branch with `git status --short --branch`.
- Confirm the current branch is **not** `main` unless `$ARGUMENTS` explicitly documents an exception.
- Run the full `/arc-qa` local QA sequence (FROM/TAG lockstep included).
- Report whether pushing the feature branch and opening/updating a PR is allowed; list blockers.
- Remind that direct pushes to `main` are forbidden.

Do not edit files, push, or open PRs.
