# Branch Policy

GitHub branch protection may be unavailable on the current plan. CI guards and local git hooks enforce the same rules.

## Protected branch

| Branch | How changes land |
|--------|------------------|
| `main` | Feature branch → PR → CI green → merge → delete feature branch |

This repo has **no `dev` branch**. If `dev` is added later, treat it like other AutomationNexus repos: feature branch PRs into `dev`, then promote to `main` via workflow — never direct push to either.

Direct `git push` to `main` is blocked locally (`.githooks/pre-push`) and fails CI if bypassed.

## Feature branch workflow

```cmd
git checkout main
git pull origin main
git checkout -b ci/short-description
REM ... edit Dockerfile.runner and/or build workflow ...
git diff --check
git push -u origin HEAD
gh pr create --base main --title "Short title" --body "Summary and test plan"
```

After CI is green on the PR, merge on GitHub. Delete the feature branch when prompted.

When bumping the runner base image, edit the `FROM` tag in `Dockerfile.runner` and the workflow `TAG` together (keep them in lockstep).

## Local hook setup (once per clone)

```cmd
tools\install-githooks.cmd
```

Or manually: `git config core.hooksPath .githooks`

Requires Git Bash (Git for Windows). The hook blocks `git push` to `main`.

## CI guard

The **Guard Main Direct Push** job in `.github/workflows/build-runner-image.yml` follows the HomeAssistant pattern: only `github-actions[bot]` may push to `main` outside a PR merge path. Do not bypass CI guards.

## Agent rules

- Never `git push origin main`.
- Never ask the user to bypass CI guards.
- Work on a feature branch; open a PR to `main`; merge only after CI is green and user approval.
