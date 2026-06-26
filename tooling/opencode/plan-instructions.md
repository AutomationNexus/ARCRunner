# ARCRunner Plan Mode

- Stay read-only in plan mode. Do not edit files.
- Never commit on `main`. Plan work for a feature branch (see `docs/runbooks/branch-policy.md`).
- This repo only changes `Dockerfile.runner` and `.github/workflows/build-runner-image.yml`; keep `FROM` and workflow tag references aligned in the plan.
- When the user approves the plan and says go, build, or execute, hand off to built-in `build` (Tab) or `/arc-execute`. Do not implement inline.
